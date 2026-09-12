# YCloud — free online deployment

## Recommended: GitHub Pages + Supabase Free

This version is designed specifically for a genuinely free college/demo deployment. It does not require Render persistent disks or a paid backend server.

### Current free-plan constraints

Supabase Free currently includes a 500 MB Postgres database, 1 GB Storage, 5 GB egress, and a 50 MB maximum file upload size. Free projects can pause after one week of inactivity. See the official pricing page before deploying. The application uploads files as 5 MB chunks, so each individual chunk stays under the 50 MB file limit.

Because YCloud stores each chunk twice, roughly half of the 1 GB storage quota is available for original user data before overhead.

## Step 1 — Create Supabase project

Create a new project on Supabase and keep it on the Free plan.

## Step 2 — Create the database/storage schema

Open **SQL Editor** and run the complete file:

```text
supabase/schema.sql
```

This creates the metadata tables, RLS policies and the private `ycloud` storage bucket.

## Step 3 — Copy API credentials

Open **Project Settings → API** in Supabase and copy:

- Project URL
- `anon` public key

Put them into:

```text
frontend/config.js
```

Example:

```js
window.YCLOUD_SUPABASE_URL = 'https://YOURPROJECT.supabase.co';
window.YCLOUD_SUPABASE_ANON_KEY = 'YOUR_PUBLIC_ANON_KEY';
```

The anon key is intended for browser use. Security comes from Supabase Auth and the Row Level Security policies in `schema.sql`. Never put a Supabase service-role key in GitHub Pages.

## Step 4 — Push to GitHub

Commit the changes and push them to your repository. Your existing GitHub Pages Action should publish the frontend.

## Step 5 — Test

Open your GitHub Pages site:

1. Register.
2. Sign in.
3. Upload an image/video/file.
4. Wait for all chunks to finish.
5. Refresh.
6. Click **Download**.

The browser downloads the stored chunks, verifies their SHA-256 checksums and reconstructs the original file.

## Online architecture

```text
             GitHub Pages
                  |
                  v
          Supabase JavaScript
                  |
       +----------+----------+
       |          |          |
       v          v          v
      Auth      Postgres   Storage
                 metadata    |
                             +-- replica-a
                             +-- replica-b
```

## What is genuinely distributed here?

The original local Docker project has three independently running storage-node containers. The free online version cannot honestly claim that it controls three separate cloud machines. Instead it uses managed Supabase Storage and stores two independent object copies for every chunk. This preserves the chunking, replication, checksum and recovery concepts without pretending there are physical nodes that you do not operate.

For a viva, describe it as **managed distributed object storage with application-level two-copy replication**. For a physical node-failure demonstration, use the Docker Compose version.
