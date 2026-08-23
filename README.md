<p align="center">
  <img src="assets/banner.svg" alt="EndoTTML Database Banner" width="100%" />
</p>

<!--
  Numbers between <!==endo:key==> markers are regenerated from the live database
  on every push by readme_stats.py. Edit the prose freely; leave the markers alone.
-->
<p align="center"><!--endo:badges-->
  <a href="https://github.com/Adityasharma0101911/EndoTTML"><img src="https://img.shields.io/badge/Database-EndoTTML-a855f7?style=for-the-badge&logo=apple-music&logoColor=white" alt="Database" /></a>
  <a href="https://github.com/Adityasharma0101911/EndoTTML"><img src="https://img.shields.io/badge/TTML%20Files-2%2C304-10b981?style=for-the-badge&logo=spotify&logoColor=white" alt="TTML Files" /></a>
  <a href="https://github.com/Adityasharma0101911/EndoTTML"><img src="https://img.shields.io/badge/Syllable%20Karaoke-1%2C546-ec4899?style=for-the-badge" alt="Syllable Karaoke" /></a>
  <a href="https://github.com/Adityasharma0101911/EndoTTML"><img src="https://img.shields.io/badge/Line%20Synced-640-3b82f6?style=for-the-badge" alt="Line Synced" /></a>
  <a href="https://github.com/Adityasharma0101911/EndoTTML"><img src="https://img.shields.io/badge/Plain%20Text-118-64748b?style=for-the-badge" alt="Plain Text" /></a>
  <a href="https://github.com/Adityasharma0101911/EndoTTML"><img src="https://img.shields.io/badge/Format-Apple%20Music%20TTML-white?style=for-the-badge&logo=apple" alt="Format" /></a>
<!--/endo:badges--></p>

<p align="center">
  <a href="https://github.com/Adityasharma0101911/EndoTTML/blob/main/LICENSE"><img src="https://img.shields.io/badge/License-MIT-purple?style=for-the-badge" alt="License" /></a>
</p>

---

## 📖 About EndoTTML Database

**EndoTTML** is a public, open-source **Timed Text Markup Language (TTML)** database engineered to standard **Apple Music** and **W3C** specifications. It provides millisecond-precise, word-by-word syllable karaoke timestamps and line-by-line synchronized lyrics for over **<!--endo:rounded-->2,300+<!--/endo:rounded--> songs**, accessible directly via global CDNs for **zero hosting costs**.

Any music player, mobile application (iOS/Android), web visualizer, or desktop app can query and stream these `.ttml` lyrics directly from this repository without maintaining a backend server.

---

## 🗂️ Tiered Quality Architecture

All lyrics in this repository are divided into 3 distinct quality tiers:

| Tier | Directory | Count | Share | Precision | Description |
| :--- | :--- | ---: | ---: | :--- | :--- |
| **Tier 1** | [`database/syllable/`](database/syllable/) | **<!--endo:syllable-->1,546<!--/endo:syllable-->** | <!--endo:syllable_pct-->67.1%<!--/endo:syllable_pct--> | `Millisecond Word / Syllable` | High-precision word-by-word Apple Music glowing karaoke spans (`<span>`). |
| **Tier 2** | [`database/line/`](database/line/) | **<!--endo:line-->640<!--/endo:line-->** | <!--endo:line_pct-->27.8%<!--/endo:line_pct--> | `Line-Level Timestamps` | Synchronized paragraph line timings (`<p begin="..." end="...">`). |
| **Tier 3** | [`database/plain/`](database/plain/) | **<!--endo:plain-->118<!--/endo:plain-->** | <!--endo:plain_pct-->5.1%<!--/endo:plain_pct--> | `Text Fallback` | Complete lyrics formatted in standard TTML structure. |

### 🔄 Automatic Quality Promotion
When a higher-tier (syllable-level) sync is discovered for a track currently classified as `line` or `plain`, the system promotes the track to `database/syllable/` and purges the lower-tier copy to maintain high database hygiene.

---

## 🌐 Serverless CDN Endpoints (How Your App Can Use This)

You can consume these lyrics from anywhere in the world using **GitHub Raw** or **jsDelivr Global Edge CDN**:

### 1. Master Search Catalog (`catalog.json`)
Contains the full JSON index of all <!--endo:total-->2,304<!--/endo:total--> tracks with artist names, track titles, albums, duration, word counts, line counts, and relative CDN paths.

```http
https://raw.githubusercontent.com/Adityasharma0101911/EndoTTML/main/catalog.json
```
*(Or via jsDelivr CDN: `https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/catalog.json`)*

### 2. Direct Syllable TTML File URL
```http
https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/database/syllable/Avicii/Wake%20Me%20Up.ttml
```

### 3. Direct Line-Synced TTML File URL
```http
https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/database/line/Coldplay/Viva%20La%20Vida.ttml
```

---

## 💻 Code Integration Examples

### 🍎 Swift / iOS (SwiftUI / MusicKit)
```swift
import Foundation

func fetchSyllableLyrics(artist: String, title: String) async throws -> String {
    let sanitizedArtist = artist.addingPercentEncoding(withAllowedCharacters: .urlPathAllowed) ?? artist
    let sanitizedTitle = title.addingPercentEncoding(withAllowedCharacters: .urlPathAllowed) ?? title
    
    let urlString = "https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/database/syllable/\(sanitizedArtist)/\(sanitizedTitle).ttml"
    guard let url = URL(string: urlString) else { throw URLError(.badURL) }
    
    let (data, _) = try await URLSession.shared.data(from: url)
    return String(data: data, encoding: .utf8) ?? ""
}
```

### 🤖 Kotlin / Android (OkHttp / Retrofit)
```kotlin
import okhttp3.OkHttpClient
import okhttp3.Request

fun getTTMLLyrics(artist: String, title: String): String? {
    val client = OkHttpClient()
    val url = "https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/database/syllable/$artist/$title.ttml"
    
    val request = Request.Builder().url(url).build()
    client.newCall(request).execute().use { response ->
        return response.body?.string()
    }
}
```

### 🌐 TypeScript / React / Web
```typescript
interface CatalogTrack {
  id: number;
  title: string;
  artist: string;
  album: string;
  sync_type: 'syllable_synced' | 'line_synced' | 'plain';
  rel_path: string;
}

// 1. Search the catalog
async function searchTrack(query: string): Promise<CatalogTrack | undefined> {
  const res = await fetch("https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/catalog.json");
  const data = await res.json();
  return data.tracks.find((t: CatalogTrack) => 
    t.title.toLowerCase().includes(query.toLowerCase())
  );
}

// 2. Stream the TTML content
async function loadTTML(relPath: string): Promise<string> {
  const res = await fetch(`https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/${relPath}`);
  return await res.text();
}
```

### 🐍 Python
```python
import requests

def get_lyrics(artist: str, title: str) -> str:
    url = f"https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/database/syllable/{artist}/{title}.ttml"
    r = requests.get(url)
    if r.status_code == 200:
        return r.text
    # Fallback to line sync
    url_line = f"https://cdn.jsdelivr.net/gh/Adityasharma0101911/EndoTTML@main/database/line/{artist}/{title}.ttml"
    return requests.get(url_line).text
```

---

## 📐 Apple Music TTML Specification Sample

Every generated `.ttml` file in this repository strictly adheres to W3C TTML and Apple Music standards:

```xml
<?xml version="1.0" encoding="utf-8"?>
<tt xmlns="http://www.w3.org/ns/ttml"
    xmlns:ttm="http://www.w3.org/ns/ttml#metadata"
    xmlns:ttp="http://www.w3.org/ns/ttml#parameter"
    xmlns:itunes="http://music.apple.com/lyric-ttml"
    ttp:timeBase="media">
  <head>
    <metadata>
      <ttm:title>Wake Me Up</ttm:title>
      <ttm:agent type="person" xml:id="v1">Avicii</ttm:agent>
      <ttm:item type="album">True</ttm:item>
      <itunes:duration>04:07.440</itunes:duration>
    </metadata>
  </head>
  <body dur="04:07.440">
    <div>
      <p begin="00:15.200" end="00:19.450" ttm:agent="v1">
        <span begin="00:15.200" end="00:15.650">Feeling </span>
        <span begin="00:15.650" end="00:15.980">my </span>
        <span begin="00:15.980" end="00:16.420">way </span>
        <span begin="00:16.420" end="00:17.100">through </span>
        <span begin="00:17.100" end="00:17.500">the </span>
        <span begin="00:17.500" end="00:18.800">darkness </span>
      </p>
    </div>
  </body>
</tt>
```

Lines sung entirely as backing vocals carry `itunes:key="role" itunes:value="background"` on the `<p>`; every other line is a lead vocal.

---

## 📊 Database Statistics

- **Total Track Records**: <!--endo:records-->2,412<!--/endo:records-->
- **Completed TTML Files**: <!--endo:total-->2,304<!--/endo:total--> (<!--endo:coverage-->95.5%<!--/endo:coverage--> hit rate)
- **Syllable-Level Karaoke Sync (Tier 1)**: <!--endo:syllable-->1,546<!--/endo:syllable--> tracks (<!--endo:syllable_pct-->67.1%<!--/endo:syllable_pct-->)
- **Line-Level Synchronized (Tier 2)**: <!--endo:line-->640<!--/endo:line--> tracks (<!--endo:line_pct-->27.8%<!--/endo:line_pct-->)
- **Plain Text Fallback (Tier 3)**: <!--endo:plain-->118<!--/endo:plain--> tracks (<!--endo:plain_pct-->5.1%<!--/endo:plain_pct-->)

> These figures and the banner above are regenerated automatically on every database push.

---

## 📜 License

This project and database are open-sourced under the **[MIT License](LICENSE)**.
TTML lyrics timestamps are provided for educational, research, and non-commercial music visualizer development.
