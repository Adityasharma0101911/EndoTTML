# EndoTTML — Open-Source Syllable TTML Database & Background Sync Engine

A complete, production-grade system for building and hosting an **open-source Apple Music TTML lyrics database on GitHub**, featuring **real-time background media watching (Spotify, Apple Music, YouTube Music)**, **tiered quality storage (`syllable/`, `line/`, `plain/`)** with **automatic upgrades**, **1-click GitHub CDN sync**, and a **standalone Windows `.exe`**.

---

## 🌟 Key Architecture & Capabilities

### 1. Zero-Cost GitHub CDN Hosting (For Any Client App)
You don't need to pay for a 24/7 database server. When you push this repository to GitHub:
- **Global CDN Access**: Any iOS, Android, Web, or Desktop app can fetch syllable TTML lyrics directly via GitHub Raw or jsDelivr CDN:
  ```http
  https://raw.githubusercontent.com/<YOUR_USERNAME>/<REPO_NAME>/main/database/syllable/Avicii/Wake%20Me%20Up.ttml
  ```
  Or via ultra-fast global edge caching:
  ```http
  https://cdn.jsdelivr.net/gh/<YOUR_USERNAME>/<REPO_NAME>@main/database/syllable/Avicii/Wake%20Me%20Up.ttml
  ```
- **Master Search Manifest (`catalog.json`)**:
  Clients can fetch [`catalog.json`](catalog.json) to instantly search all 1,800+ tracks with artist, title, album, sync precision, and CDN file paths!

---

### 2. Tiered Quality Hierarchy & Automatic Upgrades
The database is structured into 3 distinct quality tiers:
- **`database/syllable/` (Tier 1)**: Word-by-word syllable karaoke sync (`<span begin="..." end="...">`).
- **`database/line/` (Tier 2)**: Line-by-line synchronized lyrics (`<p begin="..." end="...">`).
- **`database/plain/` (Tier 3)**: Unsynchronized text fallback.

🔄 **Auto-Upgrade Logic**: If a song is currently saved under `line` or `plain`, and later a `syllable` version is detected/discovered, the system automatically deletes the lower-tier file, writes to `database/syllable/`, updates SQLite, and updates `catalog.json`!

---

### 3. Real-Time Windows Media Background Watcher
- Listens to active playback across **Spotify Desktop**, **Spotify Web**, **Apple Music**, and **YouTube Music** in Chrome/Edge/Firefox using Windows Media Controls.
- When a new song starts playing:
  1. Checks if it already has `syllable` sync in the database.
  2. If missing or only `line`/`plain`, automatically fetches and saves/upgrades the TTML in the background.
  3. Displays a Windows toast notification.

---

### 4. 1-Click GitHub Sync (Commit & Push)
- Click **"Push to GitHub"** in the Web UI or System Tray icon (or run `python main.py sync`).
- Automatically exports the latest `catalog.json`, stages all new/upgraded TTML files, creates a formatted commit message, and pushes to your GitHub repository!

---

### 5. Standalone Windows Executable (`dist/EndoTTML/EndoTTML.exe`)
- Runs in the Windows System Tray with a menu for quick actions.
- Automatically runs the media watcher and web visualizer without needing Python installed!

---

## 🚀 Quick Usage Guide

### 1. Launch System Tray App (Background Service)
```bash
python main.py tray
```
*Or double-click `dist/EndoTTML/EndoTTML.exe`.*

### 2. Launch Web Dashboard & Visualizer
```bash
python main.py web --port 8000
```
Open **[http://localhost:8000](http://localhost:8000)** in your browser.

### 3. Push Database Updates to GitHub
```bash
python main.py sync
```

### 4. View Database Statistics
```bash
python main.py stats
```

### 5. Run Quality Upgrade Check Over All Existing Songs
```bash
python main.py upgrade --workers 8
```

---

## 📂 Project Layout

```
EndoTTML/
├── database/                        # Tiered TTML Database
│   ├── syllable/                    # Tier 1: Syllable / Word-level TTML (798+ songs)
│   │   ├── Avicii/Wake Me Up.ttml
│   │   ├── Drake/One Dance.ttml
│   │   └── ...
│   ├── line/                        # Tier 2: Line-level TTML (946+ songs)
│   │   ├── Coldplay/Viva La Vida.ttml
│   │   └── ...
│   └── plain/                       # Tier 3: Plain text lyrics TTML (63 songs)
│
├── dist/EndoTTML/EndoTTML.exe       # Standalone Windows Executable
├── catalog.json                     # Master Catalog Index for External Apps
├── endo_ttml.db                     # SQLite Database
├── media_watcher.py                 # Windows Media Transport Session Watcher
├── git_sync.py                      # 1-Click Git Commit & Push Engine
├── ttml_engine.py                   # Apple Music TTML Generator & Parser
├── lyrics_fetcher.py                # Multi-tier Synced Lyrics Pipeline
├── db_manager.py                    # Tiered Storage & Upgrade Manager
├── batch_processor.py               # Batch Engine with Checkpointing
├── tray_app.py                      # Windows System Tray Application
├── web_app.py & web/index.html      # Web Dashboard & Apple Music Karaoke Player
├── build_exe.py                     # PyInstaller Build Script
└── main.py                          # CLI Interface
```

---

## 📱 How External Apps Can Consume This Database

### Example: Swift / iOS (MusicKit / AVFoundation)
```swift
let url = URL(string: "https://raw.githubusercontent.com/<USER>/<REPO>/main/database/syllable/Avicii/Wake%20Me%20Up.ttml")!
let (data, _) = try await URLSession.shared.data(from: url)
let ttmlXML = String(data: data, encoding: .utf8)
```

### Example: Kotlin / Android
```kotlin
val client = OkHttpClient()
val request = Request.Builder()
    .url("https://cdn.jsdelivr.net/gh/<USER>/<REPO>@main/database/syllable/Avicii/Wake%20Me%20Up.ttml")
    .build()
val ttmlXML = client.newCall(request).execute().body?.string()
```

### Example: TypeScript / Web / React
```typescript
const res = await fetch("https://cdn.jsdelivr.net/gh/<USER>/<REPO>@main/catalog.json");
const catalog = await res.json();
// catalog.tracks contains relative paths to all syllable and line TTML files
```
