# Worked Example: Spotify-like Music Streaming

The course's read-heavy scaling archetype. Design target starts at 500k users / 30M songs and scales to 50M users / 200M songs.

## Requirements (as scoped in the course)

- Users play songs; artists upload songs.
- Non-functional: low-latency playback start, high availability for listening, massive read volume vs tiny upload volume.

## Capacity estimates

- Avg song 3MB → 3MB × 30M songs = **90TB** audio storage
- Song metadata 100 bytes/song → **3GB**
- User metadata 1KB/user → **0.5GB**
- At 50M users / 200M songs: song metadata 100B × 200M = **20GB**; user metadata 1KB × 50M = **50GB**

Takeaway: audio dwarfs everything — it must live in blob storage, not the database. Metadata stays small enough for a relational store for a long time.

## Architecture

**Simple start:** mobile app → load balancer (availability, fault tolerance) → web servers/APIs → two stores: **blob storage** (S3/GCS/Azure) for audio files, **SQL database** for users/artists/metadata.

**Play flow:** server fetches metadata from SQL, returns a file URL; the song streams chunk-by-chunk to the app — or streams directly from object storage to the client, bypassing web servers to reduce their load.

## Schema

- **Users:** UserID, Username, Email, PasswordHash, CreatedAt, LastLogin
- **Songs:** SongID, Title, ArtistID, Duration, ReleaseDate, FileURL (points to blob storage)
- **Artists:** ArtistID, Name, Bio, Country
- **ArtistsSongs:** ArtistID, SongID (join table)

SQL fits: structured data, relationships, complex queries (browse by artist, search catalog).

## Scaling to 50M users (bottleneck-driven)

1. **Reads overwhelm the origin** → **CDN per region** (CloudFront/Cloudflare) serves popular songs close to users.
2. **Repeated plays of hits** → **LRU cache** for popular songs; unpopular songs fetched from blob, then cached.
3. **Metadata reads overwhelm the leader** → **Leader→Follower replication**: one read/write leader, multiple read-only followers (workload is overwhelmingly read-heavy).
4. Sharding or multi-leader noted as possible later steps, not needed at this scale.

## The pattern to reuse

Separate the large immutable blobs from the small relational metadata early; scale reads with CDN + cache + read replicas; let the read/write ratio pick your replication strategy.
