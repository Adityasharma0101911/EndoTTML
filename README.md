# EndoTTML — Apple Music Syllable TTML Database & Visualizer

A complete, production-grade engine and database system to generate W3C & Apple Music-compliant **TTML (Timed Text Markup Language)** files with **millisecond-precision syllable/word-by-word karaoke synchronization** from Spotify playlists and multi-tier lyrics sources.

---

## ✨ Features

- **Apple Music Syllable TTML Standard**:
  - Full W3C Timed Text Markup Language (TTML) compliance (`xmlns:itunes="http://music.apple.com/lyric-ttml"`, `ttp:timeBase="media"`).
  - High-precision word & syllable timing `<span begin="00:12.340" end="00:12.800">word </span>`.
  - Vocal agent tagging (`ttm:agent="v1"`), backing vocal roles (`itunes:key="role" itunes:value="background"`), styles, and layout regions.
- **Multi-Tier Lyrics Pipeline**:
  - **Tier 1 (Syllable / Word-Level Sync)**: Direct NetEase YRC & Enhanced LRC.
  - **Tier 2 (Line-Level Sync)**: High-speed exact & fuzzy LRCLIB API.
  - **Tier 3 (Multi-Provider Fallback)**: Deezer, Megalobiz, Lyricsify.
- **SQLite Database & Catalog**:
  - Structured storage in `endo_ttml.db` indexing track titles, artists, albums, Spotify IDs, sync types, word counts, and TTML file paths.
  - Master export to `catalog.json`.
- **Organized Folder Structure**:
  - Files saved cleanly under `database/{Artist}/{Track_Clean}.ttml`.
- **Interactive Web Player & Karaoke Visualizer**:
  - Apple Music-inspired dark theme UI with glowing word-by-word karaoke animations.
  - Real-time lyrics auto-scrolling, speed controls, scrubber timeline, raw XML code inspector, and 1-click ZIP export.

---

## 🚀 Quick Start

### 1. Launch Web Dashboard & Visualizer
```bash
python main.py web --port 8000
```
Open [http://localhost:8000](http://localhost:8000) in your browser.

### 2. View Database Statistics
```bash
python main.py stats
```

### 3. Run Batch Processing for Entire Playlist
```bash
python main.py process --workers 10
```

### 4. Generate TTML for Any Single Song
```bash
python main.py single --query "Calvin Harris - One Kiss" --save "One_Kiss.ttml"
```

### 5. Export All TTML Files as ZIP
```bash
python main.py export --zip "EndoTTML_Full_Database.zip"
```

---

## 📂 Project Architecture

```
EndoTTML/
├── database/                # Generated .ttml files organized by Artist/Track
│   ├── Avicii/
│   │   ├── The Nights.ttml
│   │   └── Wake Me Up.ttml
│   ├── Drake/
│   │   └── One Dance.ttml
│   └── ...
├── web/
│   └── index.html           # Apple Music Karaoke UI & Catalog Visualizer
├── ttml_engine.py           # Core TTML XML builder & YRC/LRC parser
├── lyrics_fetcher.py        # Multi-tier synchronized lyrics pipeline
├── db_manager.py            # SQLite database manager & indexing
├── playlist_parser.py       # Spotify playlist extractor
├── batch_processor.py       # Multi-threaded batch engine with checkpointing
├── web_app.py               # FastAPI backend server
├── main.py                  # Unified CLI interface
├── endo_ttml.db             # SQLite database
├── catalog.json             # Master exported JSON catalog
└── spotify_tracks.json      # Raw Spotify playlist tracks backup (1,816 songs)
```

---

## 📜 TTML Format Example

```xml
<?xml version="1.0" encoding="utf-8"?>
<tt xmlns="http://www.w3.org/ns/ttml" 
    xmlns:ttm="http://www.w3.org/ns/ttml#metadata" 
    xmlns:itunes="http://music.apple.com/lyric-ttml" 
    xmlns:ttp="http://www.w3.org/ns/ttml#parameter" 
    xmlns:tts="http://www.w3.org/ns/ttml#styling" 
    xml:lang="en" ttp:timeBase="media" ttp:cellResolution="32 15">
  <head>
    <metadata>
      <ttm:title>Cupid</ttm:title>
      <ttm:agent type="person" xml:id="v1">FIFTY FIFTY</ttm:agent>
      <ttm:item type="spotify_id">4k5n2h...</ttm:item>
      <itunes:duration>02:54.000</itunes:duration>
    </metadata>
    <styling>
      <style xml:id="s1" tts:color="#FFFFFF" tts:fontSize="100%" tts:textAlign="center" tts:fontFamily="sansSerif" tts:fontWeight="bold"/>
    </styling>
    <layout>
      <region xml:id="r1" tts:origin="10% 10%" tts:extent="80% 80%" tts:displayAlign="center"/>
    </layout>
  </head>
  <body>
    <div>
      <p begin="00:13.700" end="00:17.780" ttm:agent="v1">
        <span begin="00:13.700" end="00:14.540">Surrounded </span>
        <span begin="00:14.540" end="00:14.750">by </span>
        <span begin="00:14.750" end="00:15.830">couples </span>
        <span begin="00:15.830" end="00:16.280">all </span>
        <span begin="00:16.280" end="00:16.730">the </span>
        <span begin="00:16.730" end="00:17.450">time</span>
      </p>
    </div>
  </body>
</tt>
```
