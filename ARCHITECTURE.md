# Architecture Notes

## Free online mode

```text
Browser / GitHub Pages
        |
        v
Supabase Auth
        |
        +-------------------+
        |                   |
        v                   v
Postgres metadata     Supabase Storage
                            |
                       +----+----+
                       |         |
                    replica-a  replica-b
```

Each file is split into 5 MB chunks. Every chunk receives a SHA-256 checksum and two stored copies. Postgres records the file, version and chunk metadata. During download the browser tries replica A, verifies the checksum, and falls back to replica B if necessary. Chunks are assembled in order into the original file.

## Local mode

The Docker version keeps the original three independent storage containers A/B/C and FastAPI/Postgres/Redis. That version is useful for demonstrating physical node failure and replica rebalancing.

## Honest terminology

Do not describe the free online mode as three independent physical cloud storage nodes. It is a managed cloud storage service with application-level two-copy replication. This is intentional: it keeps the public deployment inside the free tier while preserving the important distributed-storage concepts.
