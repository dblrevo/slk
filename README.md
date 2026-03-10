# SLK — Word Grid Game
## GitHub Pages + Supabase (no server required)

---

## Setup — takes about 10 minutes total

### Step 1 — Create a Supabase project

1. Go to [supabase.com](https://supabase.com) and sign up (free)
2. Click **New Project**, give it a name (e.g. `slk-game`)
3. Choose a region close to you (e.g. West Europe)
4. Wait for the project to finish provisioning (~1 minute)

---

### Step 2 — Create the database table

1. In your Supabase project, go to **SQL Editor** (left sidebar)
2. Click **New query** and paste this SQL, then click **Run**:

```sql
-- Create the rooms table
create table slk_rooms (
  room_id    text primary key,
  state      jsonb not null,
  updated_at timestamptz default now()
);

-- Enable real-time on this table
alter publication supabase_realtime add table slk_rooms;

-- Allow anyone to read and write (the game has no user accounts)
create policy "Public read"  on slk_rooms for select using (true);
create policy "Public write" on slk_rooms for insert with check (true);
create policy "Public update" on slk_rooms for update using (true);
alter table slk_rooms enable row level security;
```

---

### Step 3 — Get your credentials

1. In Supabase go to **Project Settings → API** (left sidebar)
2. Copy two values:
   - **Project URL** — looks like `https://abcdefgh.supabase.co`
   - **anon / public key** — a long string starting with `eyJ...`

---

### Step 4 — Update index.html

Open `index.html` and find these three lines near the top of the `<script>` section:

```js
const SUPABASE_URL  = 'https://YOUR-PROJECT.supabase.co';
const SUPABASE_ANON = 'YOUR-ANON-PUBLIC-KEY';
const TABLE_NAME    = 'slk_rooms';
```

Replace the first two values with your actual credentials. `TABLE_NAME` stays as `slk_rooms`.

---

### Step 5 — Deploy to GitHub Pages

1. Create a GitHub repository (can be public or private)
2. Push `index.html` to the repo (it's the only file needed)
3. Go to repo **Settings → Pages**
4. Under **Source** select `Deploy from a branch`
5. Choose your branch (`main`) and folder (`/ root`)
6. Click **Save** — your game will be live in ~1 minute at:
   `https://yourusername.github.io/your-repo-name/`

---

## Playing the game

Share the URL with a room name:
```
https://yourusername.github.io/your-repo-name/?room=Saturday
```

Both players open the same URL. The first to submit a move becomes Player 1, the second becomes Player 2. Player identity is remembered in the browser for that room.

Create as many rooms as you like just by changing `?room=` — they're completely independent.

---

## How to play

- **First move**: place a word spanning all 5 cells horizontally or vertically
- **Subsequent moves**: select 1–2 empty cells on the grid, type the letters to place — they must form a word readable in any non-diagonal direction across the grid
- Use the **Notes & Scores** area to track points and words played
- The grid locks after your move until your opponent plays

---

## Notes

- Game state is stored in Supabase and **persists** — you can close the browser and return later
- Real-time sync via Supabase's built-in WebSocket subscriptions — no polling, no dropped moves
- The free Supabase tier is generous — a two-player word game uses negligible resources
- If you want multiple completely separate game instances, just use different `?room=` values
