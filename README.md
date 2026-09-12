# Distributed Cloud Storage System

YCloud is a mini distributed object-storage project with chunking, replication, integrity verification, versioning and real file reconstruction/download.

## Free online mode

The `frontend/` + `supabase/` setup is designed for GitHub Pages + Supabase Free. It supports real persistent uploads and downloads without a paid Render server.

- Supabase Auth for accounts
- Postgres for metadata
- Supabase Storage for file chunks
- 5 MB chunks
- SHA-256 chunk verification
- Two stored copies of each chunk
- Browser-side reconstruction/download

See [DEPLOYMENT.md](DEPLOYMENT.md).

## Local distributed-node mode

The original Docker Compose backend remains available for a more traditional distributed-storage demonstration with three independent storage containers, PostgreSQL and Redis:

```bash
docker compose up --build
```

Open `http://localhost:8080`.

## Important distinction

The free online deployment uses two managed object copies inside Supabase Storage; it does not operate three independently hosted physical storage servers. The Docker version is the correct one for demonstrating actual node failure and replica recovery.

## Core concepts

```text
File
  -> 5 MB chunks
  -> SHA-256 checksum
  -> replica A + replica B
  -> metadata in Postgres
  -> verified chunks downloaded
  -> browser reconstructs original file
```
