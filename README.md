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
