# CrowAI Media Player Card

*** Expermimental ***

CrowAI is a Home Assistant media player card built specifically for **iPhone**. Designed from the ground up for iPhone, it brings frosted-glass aesthetics, fluid touch animations, full Music Assistant integration, synced lyrics, a queue browser, multi-room multicast playback, rich AI-powered media info panels, and an Apple TV remote — all in a card that feels like a native iPhone app.

CrowAI is about **discovery** as much as playback — AI-powered info panels, recommendations, artist radio, similar tracks/shows/movies, and a personal Listening Recap all help you find your next favourite song, album, TV show or film, not just control what's already playing.

> ⚠️ **Music Assistant is required** for the Music Library browser, queue management, Vibe Queue Builder, AI Artist Radio, multi-room multicast playback and all MA-specific features.

> ⚠️ **Music Assistant Queue Actions is required** for the Recommended tab, full queue browsing and library drill-in. See [Music Assistant Queue Actions](https://github.com/droans/mass_queue) below.

> ⚠️ **Google Gemini is required** for all AI features — AI Info Panel, Vibe Queue Builder, AI Search, Recommendations, AI Artist Radio, Announce AI Improve and Send Message AI. See [AI Features Setup](#-ai-features-setup-google-gemini--required) below.

![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2026+-blue)
![HACS](https://img.shields.io/badge/HACS-Custom-orange)

![CrowAI Media Player Card Preview](preview.png)

[![Open your Home Assistant instance and add this repository to HACS.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=jamesmcginnis&repository=crowai-media-player-card&category=plugin)

---

## 🛠️ Installation

### Via HACS (Recommended)

1. In Home Assistant, open **HACS** from the sidebar
2. Click the **⋮ menu** (top right) → **Custom repositories**
3. Add `https://github.com/jamesmcginnis/crowai-media-player-card` as a **Lovelace** repository, then close
4. Click **+ Explore & Download Repositories**, search for **CrowAI Media Player Card** and click **Download**
5. Restart Home Assistant when prompted
6. Hard-refresh your browser (Cmd+Shift+R on Mac) or close and reopen the Home Assistant app on your iPhone

### Manual

1. Download `crowai-media-player-card.js` from [Releases](../../releases/latest)
2. Copy the file to `/config/www/` on your Home Assistant instance
3. In Home Assistant go to **Settings → Dashboards → Resources** and click **+ Add resource**
   - URL: `/local/crowai-media-player-card.js`
   - Type: **JavaScript module**
4. Click **Create**, then hard-refresh your browser

---

## 🤖 AI Features Setup (Google Gemini) — Required

All AI features are powered by **Google Gemini** via Home Assistant's conversation integration. **This setup is required for the card's core AI features to function.**

### Step 1 — Enable the Generative Language API

1. Go to [console.cloud.google.com](https://console.cloud.google.com) and sign in
2. Create a new project (or select an existing one)
3. Go to **APIs & Services → Library**
4. Search for **Generative Language API** and click **Enable**

> ⚠️ This step is essential. An API key without the Generative Language API enabled will return errors immediately.

### Step 2 — Create an API Key

1. In Google Cloud Console go to **APIs & Services → Credentials**
2. Click **+ Create Credentials → API key** and copy the key

### Step 3 — Add Google Generative AI to Home Assistant

1. In Home Assistant go to **Settings → Devices & Services → + Add Integration**
2. Search for **Google Generative AI** and select it
3. Paste your API key and click Submit
4. Click the **gear icon ⚙️** next to **Google AI Conversation**
5. Uncheck **Recommended model settings**, select **`gemini-2.0-flash`** and save

### Step 4 — Configure the Card

In the card's visual editor, find the **AI Settings** section and select **Google AI Conversation** from the AI Agent dropdown.

> 💡 Other conversation agents (Claude, OpenAI, Home Assistant's built-in AI, Ollama, etc., added via **Settings → Voice Assistants**) may also work for general-knowledge features like Media Info, but Google Gemini is the recommended and best-tested option.

### Free Tier Limits

| Model | Requests/day | Requests/min |
|-------|-------------|--------------|
| `gemini-2.0-flash` | 1,500 | 15 |
| `gemini-2.0-flash-lite` | 1,500 | 30 |

**`gemini-2.0-flash` is recommended.** The card caches all AI responses aggressively so daily quotas are rarely exhausted in normal use.

> If you see "AI rate limit reached", your daily quota is exhausted — it resets daily.

---

## 🎵 Music Assistant — Required

**Music Assistant is required** for the Music Library, queue management, Vibe Queue Builder, AI Artist Radio and multi-room multicast playback.

### Installing Music Assistant

The recommended way to run Music Assistant is as a Home Assistant **App** (formerly called an add-on), available directly in the built-in App store on Home Assistant OS installations.

1. In Home Assistant, go to **Settings → Apps**
2. Click **Install app** and search for **Music Assistant**
3. Select it, click **Install**, then start it once installed
4. After it starts, go to **Settings → Devices & Services → + Add Integration**, search for **Music Assistant** and follow the setup wizard to connect the integration to the running app
5. Open the Music Assistant web interface (via the app's page or the integration card) to add your music providers (Spotify, Apple Music, local library, Tidal, etc.) and players (HomePod, Sonos, AirPlay, Chromecast, etc.)
6. MA-managed players appear in Home Assistant as `media_player.mass_*` entities — use these in the card's `ma_entities` config

> 🍎 **Apple Music is the preferred provider.** Connect a streaming provider to Music Assistant rather than relying on a local library alone — **Apple Music is the recommended choice** for this card, giving the most reliable catalogue, metadata and artwork matches. Spotify, YouTube Music, Tidal and others are also supported, but Apple Music is the safest choice if you're picking one. Recommendations, the Vibe Queue Builder, AI Artist Radio and Similar Tracks all suggest music that needs to actually be playable through MA — a streaming provider gives them a full catalogue to draw from instead of just what you already have.

GitHub: [github.com/music-assistant](https://github.com/music-assistant) · Full documentation: [music-assistant.io](https://music-assistant.io)

### Music Assistant Queue Actions Integration — Required

The Recommended tab, Queue Browser and Library drill-in all require the [**Music Assistant Queue Actions**](https://github.com/droans/mass_queue) (`mass_queue`) integration. When installed it enables:

- Personalised recommendations in the Recommended tab
- Full queue history and upcoming tracks in the Queue Browser
- Real-time queue updates — the queue panel refreshes automatically when tracks change
- Single-item atomic reordering via `move_queue_item_next` — only the dragged track is moved
- Clean queue item removal via `remove_queue_item`
- Track listing when drilling into albums, artists, playlists and podcasts

**Installation:**

1. Open **HACS** in Home Assistant → click ⋮ → **Custom repositories**
2. Add `https://github.com/droans/mass_queue` as an **Integration** repository, then close
3. Search for **Music Assistant Queue Actions**, click **Download**
4. Restart Home Assistant — the card detects it automatically, no further configuration needed

Repository: [github.com/droans/mass_queue](https://github.com/droans/mass_queue)

---

## ✨ Features

### Core Player

- 📱 **Built for iPhone** — touch targets, long-press suppression and layout are all iPhone-first
- 🎨 **Modern design** — frosted-glass theme, rounded corners and customisable accent colours
- 📱 **Compact and expanded modes** — toggle between full album art and a space-saving mini player
- 🔄 **Automatic device switching** — card follows whichever device starts playing
- 🎵 **Full media controls** — play/pause, skip, shuffle, repeat, seek via progress bar
- ⏩ **Double-tap to seek** — double-tap left or right of artwork to seek −15s or +15s
- 🖼️ **Artwork tap actions** — single-tap opens AI Info (music) or Media Info (TV/movies); double-tap the left or right side to seek −15s/+15s; long-press opens lyrics
- 🖼️ **Artwork zoom** — tap the mini album art in the AI Info panel or album view to see a larger version
- ✨ **Tactile button feedback** — glow and blur effects when buttons are pressed
- 🔊 **Volume control** — slider or +/− buttons with optional routing to a separate volume entity
- 📊 **Volume HUD** — sleek overlay showing current level; fades after 1.5 seconds
- 🔇 **Mute toggle** — tap the volume badge or speaker icon to instantly mute/unmute
- 🖼️ **Album artwork** — automatic iTunes artwork lookup when no artwork is provided
- 🎨 **Artwork Crossfade** — optional cinematic fade-to-black transition between tracks
- 🎛️ **Player Icon Themes** — eight icon sets: Standard, Modern, Robot, Chunky, Retro, Sharp, Pixel and LCD
- 🌈 **Ambient glow** — extracts dominant colour from artwork and applies a subtle glow
- 📻 **Radio mode indicator** — tap the radio icon to turn radio mode off immediately
- 📻 **HA Radio Browser integration** — browse Home Assistant's own Radio Browser categories (Popular, By Country, By Genre, etc.) directly from the Radio tab, alongside direct radio-browser.info search
- 🏷️ **Live/Podcast/Audiobook pill** — optional badge on the artwork screen identifying what's currently playing (off by default)
- 📋 **Multi-device support** — manage Apple TV, HomePod and Music Assistant speakers from one card
- 🎯 **Live progress tracking** — smooth real-time position updates
- 💬 **In-card notifications** — toast alerts appear inside the card
- 🎨 **Controls Theme** — 12 colour presets for the control icons (Classic, Vivid, Warm, Ocean, Rose, Forest, Neon, Soft, Midnight, Gold, Retro, Ember)

### AI Features

- ✨ **AI Info Panel** — tap the artwork while music plays: year, label, length, fun fact, genre tags, band members / artist section, similar tracks; all AI-generated and cached per track
- 💬 **Song Intro** — a short, intriguing one-line fact about the playing track appears below the artist name a few seconds after it starts, then fades away; off by default, toggle in AI Settings
- 🎭 **Vibe Queue Builder** — 100+ vibes across Energy, Calm, Focus, Mood, Social, Decades, Genre, Time, Seasons and Binaural & Noise; builds a themed MA queue instantly
- 🔍 **AI Search** — natural language search across your MA library
- 🌟 **Recommendations** — AI-curated track suggestions based on what's playing
- 📻 **AI Artist Radio** — builds a continuous radio queue around any artist
- 🎵 **Similar Tracks** — shown in the AI Info panel; tap to drill in, long-press for the enqueue menu
- 👥 **Band Members / Artist** — tap any member for their bio with photo, fun fact and Known For songs; tap the bio photo to zoom
- 💬 **Announce AI Improve** — rewrites your announcement naturally (requires Gemini)
- 📨 **Send Message AI** — improves your notification text (requires Gemini)
- 📚 **Audiobook search** — find free, public-domain audiobooks via LibriVox/Archive.org, with AI-assisted query refinement (requires Gemini)
- 🎙️ **Podcast search** — search iTunes for podcasts and pin your favourites
- 📊 **Listening Recap** — a personal weekly snapshot: top artists and tracks (last 7 days, up to 10 each) plus a short AI-written summary that regenerates fresh every time you open it

### Persistent Storage

By default, pins, AI lookups, iTunes artwork, Wikipedia photos and lyrics are cached in the browser only, which means they can be lost to WKWebView cache evictions or app restarts. Each of these can individually be switched to **persistent storage** in **Caches & Data**, which saves them to Home Assistant's own database instead:

- **iTunes Artwork**, **Wikipedia Artwork**, **Pinned Items**, **AI Info** (track info, bios, recommendations, etc.) and **Lyrics** each have their own Persistent Storage toggle
- Persistent data is loaded once per session and merged with the on-device cache — Home Assistant's copy always wins on conflict
- A **Clear Persistent Storage** button (separate from the regular cache-clear buttons) removes everything saved this way

### Vibe Queue Builder

100+ vibes across 10 categories:

| Category | Examples |
|----------|---------|
| Energy | Hype, Workout, Epic, Chill Drive |
| Calm | Meditate, Sleep, Rainy Day, Wind Down |
| Focus | Study, Deep Work, Coding, Flow State |
| Vibe | Happy, Melancholy, Nostalgic, Romantic |
| Social | Party, Pub Night, BBQ, Pre-Drinks |
| Decades | 50s through 2020s |
| Genre | Rock, Jazz, Classical, Hip Hop, Electronic, and 20+ more |
| Time | Morning, Evening, Late Night, Midnight |
| Seasons | Summer, Autumn, Winter, Spring |
| Binaural & Noise | White Noise, Pink Noise, Delta Waves, Focus Waves |

Decade moods search your MA library for chart compilations first, then fall back to streaming with one track per seed artist. Genre and Social moods search for curated playlists, falling back to radio mode. All seeds and search terms are fully customisable in the visual editor.

### Quick Menu (⋮)

| Item | Notes |
|------|-------|
| Queue | MA speakers only |
| AI Search | Natural language MA search |
| Music Library | MA speakers only |
| Vibe | Opens Vibe Queue Builder |
| Recommendations | AI track suggestions |
| Listening Recap | Personal weekly top artists/tracks with an AI summary |
| Add Similar Songs | Adds AI-suggested tracks to queue |
| AI Artist Radio | Builds artist radio queue |
| Radio Mode | Toggles MA radio mode |
| Lyrics | Toggles lyrics panel |
| Announce | TTS announcement panel |
| Send Message | Push notification panel |
| Share | Copies track info + a link to your chosen music service (see [Sharing](#sharing)) |
| More Info | Opens AI/Media Info panel |

### Multi-Room Multicast

- Play the same audio on multiple MA speakers simultaneously
- **Speaker pills** — always visible; one per active grouped speaker; locked while a queue is building
- **Independent volume** — tap any pill to focus and control that speaker
- **Sync volumes** — match all grouped speakers to the focused speaker's volume
- **+ Add** pill appears when additional MA speakers are available

### Pinning (Library)

Pin your favourites for one-tap access. All pins also live together in a consolidated **Pinned** section in the Music Library, with its own sub-categories: Songs, Artists, Albums, Playlists, Queues, Radio, Podcasts and Audiobooks.

- **What can be pinned** — radio stations, podcasts, audiobooks, saved queues (see [Pin Queue as Playlist](#pin-queue-as-playlist)), and MA library tracks, artists, albums and playlists
- **How to pin** — long-press any item (or use the pin icon in its info panel) and choose Pin/Unpin
- **Show Pins in Sections** *(on by default)* — when enabled, pinned items also still appear inline at the top of their own tab (e.g. Pinned Songs in the Songs tab); turn it off in **Caches & Data** so pins only appear in the consolidated Pinned section
- **Management** — view and clear pins individually or all at once from **Caches & Data → Pinned Items** in the visual editor
- Pins are stored on-device only by default and aren't synced between browsers or devices
- Enable **Persistent Pin Storage** in **Caches & Data** to also save pins to Home Assistant's database, so they survive app restarts and cache clears
- Clearing caches (individually or via Clear All Caches) still clears on-device pins — use **Clear Persistent Storage** to also remove the HA-side copy

### Pin Queue as Playlist

Save the current queue as a named snapshot from the queue's 3-dot menu — **Pin Queue as Playlist**. It's saved as a point-in-time copy (not a live link to the original queue) and shows up under **Queues** in the consolidated Pinned section, ready to play back in full any time.

### Listening Recap

Open from the quick menu (below Recommendations) for a personal snapshot of your recent listening.

- **Top Artists & Top Tracks** — up to 10 each, ranked by play count over a rolling last-7-days window
- **AI summary** — a short, warm write-up of your week's listening, regenerated fresh every time the panel opens (not cached, so it always matches the numbers below it)
- **Tap a track** to open its AI Info panel; **tap an artist** to open their bio — the same panels used throughout the card
- **What counts as a play** — a track has to play past 30 seconds or half its duration (whichever is smaller) to be logged, so quick skips don't pollute your stats
- **What's excluded** — radio streams, podcasts, audiobooks, and system/notification sounds (e.g. announcements) never count toward your Recap
- **Which entities count** — any entity listed in this card's `entities` or `ma_entities` config, not MA-only. Whichever one is actively reporting as playing at a given moment is used, so a speaker with both a native and an MA entity is covered by either
- **Shared across rooms on the same device** — logging is scoped to whatever entities a card is configured with, but the Recap panel itself doesn't filter by entity when displaying results. If you run separate cards per room on the same phone/browser, they all read from one shared history, so every card's Recap shows the combined total of everything any of your cards have logged — not a breakdown per room
- **Catches up on missed plays** — each time a card loads, it also pulls that entity's own state history from Home Assistant (not just what it observes live) to backfill anything played while that card wasn't open — e.g. a different room's tab was active at the time. This needs Home Assistant's recorder to actually be tracking the entity; it looks back up to 48 hours on a card's first-ever load, then only as far as its last check after that
- **Per-user** — if you and other household members use separate Home Assistant accounts, each person's Recap is tracked and stored separately, the same way Pins are
- **Clear Recap History** — a button at the bottom of the panel (with an iOS-style confirmation) permanently deletes your Recap history and resets the history-backfill checkpoint, so cleared plays don't get silently reinstated the next time a card loads; enable **AI Info Persistent Storage** in **Caches & Data** so this history survives app restarts and WKWebView cache evictions

### Synced Lyrics

Long-press the artwork while music plays to open the full-screen lyrics panel.

- Real-time line highlighting with auto-scroll
- Plain lyrics fallback when timestamps aren't available
- Background prefetch — opens instantly
- Double-tap to pause/resume auto-scrolling
- Auto-close when track ends or changes

### Sharing

Share is available from the quick menu, the AI Info / Media Info panels, and the long-press menu on any queue row. It copies everything to the clipboard — there's a toast confirmation when it's done.

- **Music** — copies the track title and artist (plus album, where shown) along with a link to find the track on your chosen streaming service
- **Movies & TV** — copies the title, year and a short synopsis along with a link to find it on TheMovieDB
- **Choose your service** — pick the destination for music links in **AI Settings**: YouTube Music (default), Apple Music, Spotify, Tidal, Amazon Music or Deezer

### Media Info Panels

**Single-tap** the artwork to open:

**Music — AI Info Panel:**
- Year, label, length, fun fact, genre tags
- Album pill — tap to open the album and browse its tracks, with its own action bar; tap album art to zoom
- Band Members / Artist — tap any member to open their bio with photo (tap to zoom), Known For songs and fun fact
- Similar Tracks — tap to drill in, long-press for enqueue menu
- Mini album art — tap to view a larger version
- Action bar: Play Now, Add, Play Next, Add Album

**TV Shows:**
- Poster (tap to zoom), genre tags, overview, cast
- Similar Shows — same idea as Similar Tracks; tap any to drill straight into that show's info
- Season and episode browser with formatted airdates
- Full back-navigation from episode detail back to Media Info

**Movies:**
- Poster (tap to zoom), title, year, genre tags, synopsis, cast
- Similar Movies — same idea as Similar Tracks; tap any to drill straight into that movie's info

**Cast navigation:** tap any cast member to open their bio with photo (tap to zoom), Known For credits and a fun fact — the same related-content pattern used throughout the card. Tap their photo in the bio to see a larger version.

### Queue Panel

- Now Playing row with animated sound bars — tap to open AI Info
- Drag to reorder (MA only, requires Queue Actions)
- Long-press any row: Play Now, Play Next, Add to Queue, AI Artist Radio, Share, More Info
- Queue 3-dot menu: Music Library, Mood, AI Artist Radio, Radio Mode, Announce, Send Message, **Pin Queue as Playlist**, Clear Queue

### Music Assistant Library Browser

Tabs: Recently Added, Pinned, Favourites, Made for You (Recommended), Playlists, Artists, Albums, Songs, Radio, Podcasts, Audiobooks. (Classic tab-bar mode shows Recently Played instead of Recently Added/Pinned — toggle the layout in **Appearance & Behaviour**.)

- Tap any item to play; drill into collections with a back button
- Action bar: Play All, Add to Queue, Play Next
- Long-press tracks for the enqueue menu
- **Podcasts tab** — search iTunes directly; pin favourites
- **Audiobooks tab** — search free, public-domain titles on LibriVox via the Archive.org catalogue, with AI-assisted query refinement and chapter-by-chapter playback; pin favourites
- **Radio tab** — search radio-browser.info directly, or use **Browse Home Assistant Radio** to explore categories from HA's own Radio Browser integration (requires the [Radio Browser](https://www.home-assistant.io/integrations/radio_browser/) integration under **Settings → Devices & Services**)
- **Pinned tab** — everything you've pinned, grouped into Songs, Artists, Albums, Playlists, Queues, Radio, Podcasts and Audiobooks

### Apple TV Remote

| Button | Action |
|--------|--------|
| **Back** | Navigate back / menu |
| **TV** | Home screen / wakes from sleep |
| **Power Off** | Sends `suspend` to sleep the Apple TV |

---

## 🔧 Visual Editor

The editor includes a filter box at the top (search any setting by name) and a **Reset All Settings to Defaults** button. Several sections are collapsible — tap the header to expand.

| Section | Settings |
|---------|---------|
| Manage & Reorder Media Players | Drag-and-drop reorder; enable/disable; startup volume; MA speaker toggle — all per entity |
| Appearance & Behaviour *(collapsible)* | Follow HA Theme, Auto Switch, Remember Last Speaker, Media Player Selector, Music Library Layout (iOS-style category list vs. classic tab bar), Always Show Library Button, Show Remote Button, Default Radio Mode on Startup, iTunes Artwork Fallback, Show Volume HUD, Live/Podcast/Audiobook Pill, Volume Buttons, Volume Percentage, Scroll Long Text, Lyrics persistence and caching, and a **Startup & Navigation** sub-section (Startup View, Retain Current View, Remote Button Row Position, Volume Entity) |
| Caches & Data *(collapsible)* | AI caches (bios, trivia, where-to-watch, content warnings, year-in-music, vibe history, AI response cache), artwork caches (iTunes, Wikipedia) with Persistent Storage toggles, library & radio caches (MA library, radio stations, HA registry), lyrics cache & scroll style with its own Persistent Storage toggle, and management of all pinned items including a **Show Pins in Sections** toggle, a Persistent Pin Storage toggle and a Clear Persistent Storage button |
| Visual Effects | Card Liquid Glass, Remote Liquid Glass, Volume HUD Liquid Glass, Ambient Glow, Library & Queue Row Glow, Artwork Crossfade, Resize Button Spin |
| ✨ AI Settings | AI Agent selector, Share Track service (YouTube Music, Apple Music, Spotify, Tidal, Amazon Music, Deezer), Announce TTS Service, Song Intro toggle |
| AI Vibe Artist Seeds | Playlist search terms and radio fallback artist per vibe; fully customisable |
| Colours & Themes *(collapsible)* | Controls Theme (12 presets), Player Icon Theme (8 sets), accent, volume accent, title, artist, button, +Add pill, volume %, custom background and lyrics colours, with live preview |

---

## 📋 Quick Start

```yaml
type: custom:crowai-media-player-card
entities:
  - media_player.living_room_apple_tv
  - media_player.living_room_homepod
  - media_player.mass_living_room
ma_entities:
  - media_player.mass_living_room
ai_conversation_agent: conversation.google_generative_ai
accent_color: '#007AFF'
controls_theme: classic
startup_volume: 35
use_ha_theme: false
lyrics_scroll_mode: highlight
lyrics_cache_enabled: true
lyrics_cache_ttl: 7
ma_library_cache_enabled: true
ma_library_cache_ttl: 1
ma_radio_mode: false
icon_theme: robot
artwork_crossfade: false
ambient_glow: false
row_glow: false
show_remote_button: true
show_media_type_pill: false
song_intro_enabled: true
card_liquid_glass: true
ma_ios_library: true
show_pins_in_sections: true
```

> **Note:** `ma_entities` should list your MA speaker entities (e.g. `media_player.mass_kitchen_homepod`). These do **not** need to also appear in `entities`.

---

## 🔧 Troubleshooting

**AI features show "Could not build queue" or "AI rate limit reached"**
- Your Gemini daily quota (1,500 requests for `gemini-2.0-flash`) is exhausted. It resets daily.

**AI features show "No AI agent found" or don't work**
- Ensure the **Generative Language API** is enabled in Google Cloud Console — this is the most common setup mistake.
- Confirm **Google AI Conversation** is selected as the AI Agent in the visual editor.

**Vibe queue plays only one song or none**
- Ensure your MA speaker entity has the **Music Assistant Speaker** toggle enabled in the visual editor.
- The card needs the `media_player.mass_*` entity, not the native HomePod or Apple TV entity.

**Speaker pills stay greyed out after a queue builds**
- This should clear automatically when the queue finishes. If not, a page refresh will always restore normal state.

**Custom colours don't work**
- Turn off **Follow Home Assistant Theme** in the visual editor under Appearance & Behaviour.

**Card doesn't appear after installation**
- Add the resource to Lovelace and hard-refresh on your iPhone (close and reopen the HA app).

**Lyrics don't open**
- Long-press the artwork while a music track is actively playing.

**Send Message shows no devices**
- Install the Home Assistant Companion App on your iPhone and log in.

**Apple TV won't turn on from Power Off**
- Use the **TV button** instead — it wakes Apple TV from sleep reliably.

**"Can't browse Home Assistant Radio" in the Radio tab**
- Add the [Radio Browser](https://www.home-assistant.io/integrations/radio_browser/) integration under **Settings → Devices & Services** in Home Assistant, then reopen the Radio tab.

**Card feels sluggish or iPhone gets warm**
- This is expected during intensive AI operations. Avoid triggering multiple AI features simultaneously. Subsequent loads of cached content are near-instant.

---

## 🙏 Credits & Acknowledgements

- The [Home Assistant](https://www.home-assistant.io) team
- The HA community for inspiration and feedback
- All users who test, report issues and suggest improvements
- My Loving Wife for her endless support ❤️

---

## 📄 License

MIT License — free to use, modify and distribute.

---

## 🐦 Why "CROW"?

- **C**lean design
- **R**esponsive interface
- **O**ptimised performance
- **W**ell-crafted experience

---

## ⭐ Support

If this card is useful to you, please **star the repository** and share it with the community!

For bugs or feature requests, use the [GitHub Issues](../../issues) page.
