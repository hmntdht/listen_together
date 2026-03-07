# 🎵 SyncWave — Listen Together in Perfect Sync

A real-time synchronized music player built with vanilla HTML/CSS/JS and Supabase. One host controls playback; all listeners sync automatically.

---

## ✨ Features

- 🏠 **Room System** — Host creates a room with a unique code (e.g. `WAVE-4821`), listeners join with it
- 🎵 **Multi-track Queue** — Drag & drop or click to upload multiple audio files
- ▶ **Full Host Controls** — Play, pause, seek, previous/next track
- 🔄 **Perfect Sync** — Listeners auto-correct drift every 5 seconds using:
  ```
  currentPosition = savedTimestamp + (Date.now() - lastUpdated) / 1000
  ```
- 👥 **Live User List** — See who's connected in the sidebar
- 📱 **Mobile Responsive** — Works on phones and tablets

---

## 🚀 Deployment (GitHub Pages)

1. Fork or clone this repo
2. Go to **Settings → Pages → Source → `main` branch → `/root`**
3. Your app will be live at: `https://yourusername.github.io/your-repo/`

Since everything is in `index.html`, no build step is required.

---

## 🔧 Supabase Setup

### 1. Create a free Supabase project

Go to [https://supabase.com](https://supabase.com) → New Project

### 2. Run this SQL in the SQL Editor

```sql
-- Create the rooms table
CREATE TABLE rooms (
  id            UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  code          TEXT NOT NULL UNIQUE,
  host_id       TEXT NOT NULL,
  host_name     TEXT NOT NULL DEFAULT 'Host',
  is_playing    BOOLEAN DEFAULT false,
  position      FLOAT DEFAULT 0,
  last_updated  BIGINT DEFAULT 0,
  track_name    TEXT DEFAULT '',
  users         TEXT DEFAULT '[]',
  created_at    TIMESTAMPTZ DEFAULT now()
);

-- Enable Row Level Security but allow all operations (public app)
ALTER TABLE rooms ENABLE ROW LEVEL SECURITY;

CREATE POLICY "Allow all reads"   ON rooms FOR SELECT USING (true);
CREATE POLICY "Allow all inserts" ON rooms FOR INSERT WITH CHECK (true);
CREATE POLICY "Allow all updates" ON rooms FOR UPDATE USING (true);

-- Enable Realtime for the rooms table
ALTER PUBLICATION supabase_realtime ADD TABLE rooms;
```

### 3. Get your API credentials

In your Supabase dashboard:
- Go to **Settings → API**
- Copy **Project URL** (looks like `https://xxxx.supabase.co`)
- Copy **anon public** key (the long JWT string)

### 4. Enter credentials in the app

Click the **⚙** button (top-right) in SyncWave and paste your URL + key. They're saved to `localStorage` so you only do this once per browser.

---

## 🎧 How to Use

### As Host
1. Enter your name → Click **Create Room**
2. Share the room code (e.g. `WAVE-4821`) with friends
3. Upload audio files via the upload area or drag & drop
4. Press ▶ to start — all listeners will sync instantly

### As Listener
1. Enter your name + the room code → Click **Join Room**
2. The player will automatically sync with the host
3. Adjust your local volume with the slider

---

## 🔄 Sync Logic

```javascript
// Host stores state on every play/pause/seek and every 5 seconds:
{
  is_playing:   true,
  position:     42.3,          // seconds
  last_updated: 1712345678000  // Unix ms timestamp
  track_name:   "Song Title"
}

// Listeners calculate:
const elapsed   = (Date.now() - room.last_updated) / 1000;
const targetPos = room.position + (room.is_playing ? elapsed : 0);

// If drift > 1.5 seconds → auto-correct
if (Math.abs(audioEl.currentTime - targetPos) > 1.5) {
  audioEl.currentTime = targetPos;
}
```

---

## ⚠️ Notes

- **Audio files are local** — each device needs the same files. The host uploads them; listeners will need to upload the same files too (or use a shared source like a URL in a future version).
- For a **shared audio URL** approach: replace file upload with a URL input pointing to a public CDN-hosted file — all devices can then stream the same source.
- Room cleanup: Old rooms persist in Supabase. You can add a cron job or Supabase Edge Function to delete rooms older than 24h.

---

## 🗂️ File Structure

```
/
└── index.html    ← Entire app (HTML + CSS + JS)
└── README.md     ← This file
```

---

## 🛠️ Tech Stack

| Layer       | Technology           |
|-------------|----------------------|
| Frontend    | HTML, CSS, JavaScript (Vanilla) |
| Realtime    | Supabase Realtime (postgres_changes) |
| Database    | Supabase PostgreSQL  |
| Hosting     | GitHub Pages         |
| Fonts       | Google Fonts (Syne + DM Sans) |

---

## 📄 License

MIT — free to use and modify.
