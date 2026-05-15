# Seed Mock Data

Seed the local Docker Postgres database with realistic mock data for all
Verrlo features (users, profiles, matches, messages, communities, bug reports,
reported users, feedback, saved filters, blocked users, roommate details).

## Context

- Backend repo: `~/OneDrive/Documents/GitHub/backend`
- App container: `backend-app-1`
- DB container: `backend-db-1`
- Seed file: `src/database/seed.ts`
- Database: `room-mates` (Postgres)

## Steps

### 1. Verify containers are running

```bash
docker ps --format 'table {{.Names}}\t{{.Status}}'
```

Both `backend-app-1` and `backend-db-1` must show `Up`. If not, run:

```bash
cd ~/OneDrive/Documents/GitHub/backend && docker compose up -d
```

### 2. Add missing column if needed

The `last_active_at` column may not exist if migrations haven't been run:

```bash
docker exec backend-db-1 psql -U postgres -d room-mates \
  -c 'ALTER TABLE profile ADD COLUMN IF NOT EXISTS last_active_at TIMESTAMP;'
```

### 3. Copy the latest seed file into the container

Always copy fresh — the container's copy may be stale:

```bash
docker cp ~/OneDrive/Documents/GitHub/backend/src/database/seed.ts \
  backend-app-1:/app/src/database/seed.ts
```

### 4. Run the seed

```bash
docker exec backend-app-1 npx ts-node -r tsconfig-paths/register src/database/seed.ts
```

### Expected output

The final `info` log line should read:

```
Seeded: 75 users | 15 mutual matches with messages | 5 pending reach-outs |
5 declined matches | 15 bug reports | 8 reported users | 12 feedback entries |
saved filters for first 15 users | 4 communities with members + messages |
3 blocked pairs | 20 roommate-detail rows
```

## Notes

- The seed **truncates all tables first** — run it freely, it is fully idempotent.
- `last_active_at` is in the Drizzle schema but may be missing from the DB if
  the migration hasn't been applied; the `ALTER TABLE … IF NOT EXISTS` in
  step 2 is safe to run every time.
- The `saved_filters` and `roommate_details` inserts use raw SQL because the
  Drizzle schema column names differ from the live DB column names.
