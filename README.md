# CrowAI Media Player Card

*** Experimental ***

CrowAI is a Home Assistant media player card built specifically for **iPhone**. Designed from the ground up for iPhone, it brings frosted-glass aesthetics, fluid touch animations, full Music Assistant integration, synced lyrics, a queue browser, multi-room multicast playback, rich AI-powered media info panels, and an Apple TV remote — all in a card that feels like a native iPhone app.

CrowAI is about **discovery** as much as playback — AI-powered info panels, recommendations, artist radio, similar tracks/shows/movies, AI-interpreted library search, and personal Music Recap / Video Recap recaps all help you find your next favourite song, album, TV show or film, not just control what's already playing.

> ⚠️ **Music Assistant is required** for the Music Library browser, queue management, Vibe Queue Builder, AI Artist Radio, multi-room multicast playback and all MA-specific features.

> ⚠️ **Music Assistant Queue Actions is required** for the Recommended tab, full queue browsing and library drill-in. See [Music Assistant Queue Actions](https://github.com/droans/mass_queue) below.

> ✨ **AI features are optional and off by default.** Turn on **Enable AI Features** in the editor's AI Settings to unlock them — they need a conversation agent such as Google Gemini (see [AI Features Setup](#-ai-features-setup-optional) below). With AI off, the info panel shows **Discogs** data instead and everything else in the card works as normal.

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

## 🤖 AI Features Setup (Optional)

AI features are **off by default** — the card works fully without them, using Discogs for the info panel. To unlock the AI features (AI Info Panel, Vibe Queue Builder, AI Search, Recommendations, AI Artist Radio, Song Intro, Music/Video Recap summaries, Announce AI Improve and Send Message AI), turn on **Enable AI Features** at the top of the editor's **AI Settings** section, then set up a conversation agent. **Google Gemini** is the recommended and best-tested agent:

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

In the card's visual editor, open the **AI Settings** section, turn on **Enable AI Features**, and select **Google AI Conversation** from the AI Agent dropdown.

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
- 💗 **Double-tap to pin** — double-tap the *center* of the artwork to pin whatever's currently playing — a song, movie, TV show, or radio station — with a burst of red hearts floating up from the tap point (grey for unpinning; the heart burst can be turned off with the **Pin Hearts** toggle in Visual Effects, the pin itself always works). For radio, this uses the same station identification as the LIVE pill, so it only works once the station's been resolved (usually near-instant, but a station that's never been looked up this session may need a moment, or a tap of the LIVE pill first)
- 📍 **Pinned indicator** — a small pin badge appears in the bottom-left corner of the artwork whenever the currently playing track/movie/show/radio station is pinned, and updates live regardless of which of the card's several pin buttons was used. Tap it to unpin — shows an iOS-style confirmation first, then the same grey heart-burst as unpinning via double-tap
- 🖼️ **Artwork tap actions** — single-tap opens the info panel (music: AI Info or Discogs; TV/movies: Media Info; live radio with track metadata: that track's info); double-tap the left or right edge to seek −15s/+15s, double-tap the center to pin; long-press opens lyrics
- 🖼️ **Artwork zoom** — tap the mini album art in the AI Info panel or album view to see a larger version
- ✨ **Tactile button feedback** — glow and blur effects when buttons are pressed
- 🔊 **Volume control** — slider or +/− buttons with optional per-speaker routing to a separate volume entity (e.g. an amp or receiver), configured on that speaker's own settings page in the editor
- 🏷️ **Speaker Display Names** — give any speaker a short friendly name on its settings page in the editor; it's used everywhere the card shows a speaker (speaker menu, summary pill, group sheets, Announce, toasts) while Home Assistant keeps the real name
- 📊 **Volume HUD** — sleek overlay showing current level; fades after 1.5 seconds
- 🔇 **Mute toggle** — tap the volume badge or speaker icon to instantly mute/unmute
- 🖼️ **Album artwork** — automatic iTunes artwork lookup when no artwork is provided
- 🎨 **Artwork Crossfade** — optional cinematic fade-to-black transition between tracks
- 🎛️ **Player Icon Themes** — eight icon sets: Standard, Modern, Robot, Chunky, Retro, Sharp, Pixel and LCD
- 🌈 **Ambient glow** — extracts dominant colour from artwork and applies a subtle glow
- 📻 **Radio mode indicator** — tap the radio icon to turn radio mode off immediately
- 🔴 **Live station identification** — a LIVE pill appears on the artwork while a radio stream plays, on any speaker type (MA or native); tap it to see the station's format, country, votes, website and description, and the artwork automatically resolves to the station's own logo when available. Stations played straight from the Music Assistant library are identified from MA itself, so their panel opens (fully playable and pinnable) even when radio-browser.info doesn't know them. When a station broadcasts real track metadata (artist + title), tapping the *artwork* opens that track's own info panel with the station shown as a badge — the LIVE pill itself always opens the station panel
- 📻 **HA Radio Browser integration** — browse Home Assistant's own Radio Browser categories (Popular, By Country, By Genre, etc.) directly from the Radio tab, alongside direct radio-browser.info search
- 🏷️ **Live/Podcast/Audiobook pill** — optional badge on the artwork screen identifying what's currently playing (off by default)
- 📋 **Multi-device support** — manage Apple TV, HomePod and Music Assistant speakers from one card
- 🎯 **Live progress tracking** — smooth real-time position updates
- 💬 **In-card notifications** — toast alerts appear inside the card
- 🎨 **Controls Theme** — 12 colour presets for the control icons (Classic, Vivid, Warm, Ocean, Rose, Forest, Neon, Soft, Midnight, Gold, Retro, Ember)

### AI Features

- ✨ **AI Info Panel** — tap the artwork while music plays: year, label, length, fun fact, genre tags, band members / artist section, similar tracks; all AI-generated and cached per track
- 💿 **Discogs Panel** — the default info panel when AI features are off, and the automatic fallback when AI can't identify a track: year, label, length, tappable genre tags, artist section and a full tracklist with community rating, in the same panel layout. The header reads "Discogs Info" so it's clear where the data came from; tapping a tracklist row opens that track's own info, same as everywhere else in the card. Built-in rate-limit protection backs off automatically for 10 seconds whenever Discogs asks the card to slow down.
- 💬 **Song Intro** — a short, intriguing one-line fact about the playing track appears below the artist name a few seconds after it starts, then fades away; off by default, toggle in AI Settings
- 🎭 **Vibe Queue Builder** — 100+ vibes across Energy, Calm, Focus, Mood, Social, Decades, Genre, Time, Seasons and Binaural & Noise; builds a themed MA queue instantly
- 🔍 **AI Search** — natural language search, up to 18 results per query; available as a standalone panel, a box at the top of the library, and a dedicated AI search button next to the Songs, Artists and Albums tab search bars (each returns matching tracks, artists or albums respectively, on top of the tab's normal exact-match search)
- 🕓 **Recent Searches** — a category at the top of the library listing every search you've run (Music Assistant, iTunes-backed searches like Podcasts/Movies & TV/Radio, and AI searches alike), capped at 50 and always sorted most-recent-first; tap an entry to re-run it, with a Clear option (iOS-style confirmation) to wipe the list
- 🌟 **Recommendations** — AI-curated track/movie/show suggestions based on what's playing, up to 18 results
- 📻 **AI Artist Radio** — builds a continuous radio queue around any artist
- 🎵 **Similar Tracks / Similar Movies & Shows** — up to 10 shown in the AI Info / Media Info panels; tap to drill in, long-press for the enqueue menu
- 👥 **Band Members / Artist** — tap any member for their bio with photo, fun fact and Known For songs; tap the bio photo to zoom
- 💬 **Announce AI Improve** — rewrites your announcement naturally
- 📨 **Send Message AI** — improves your notification text
- 📚 **Audiobook search** — find free, public-domain audiobooks via LibriVox/Archive.org, with AI-assisted query refinement when AI features are enabled (plain search works without)
- 🎙️ **Podcast search** — search iTunes for podcasts and pin your favourites
- 🎬 **Find Soundtrack** — available from the media player's quick menu while watching on Apple TV, and from the long-press menu on any Pinned Movie/TV Show — opens an AI-powered search for "Music from [title]"
- ▶️ **Open on YouTube / Trailer** — a button in the AI Info / Media Info panel header, next to Share: opens a YouTube search for the current song, or the trailer for a movie/TV show. Always searches YouTube specifically regardless of your configured Share service. Toggle in **Appearance & Behaviour**
- 📊 **Music Recap** — a personal weekly snapshot: top artists and tracks (last 7 days, up to 10 each) plus a short AI-written summary that regenerates fresh every time you open it
- 🎬 **Video Recap** — the same idea as Music Recap, but for movies and TV shows: top shows and top movies over the last 7 days plus a fresh AI-written summary every time you open it

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
| AI Search | Natural language search, up to 18 results |
| Music Library | MA speakers only |
| Vibe | Opens Vibe Queue Builder |
| Add Similar Songs | Adds up to 18 AI-suggested tracks to queue |
| Add Album | Adds the currently playing track's album to the queue |
| Recommendations | AI track/movie/show suggestions, up to 18 results |
| Music Recap | Personal weekly top artists/tracks with an AI summary |
| Video Recap | Personal weekly top shows/movies with an AI summary |
| AI Artist Radio | Builds artist radio queue |
| Radio Mode | Toggles MA radio mode |
| Lyrics | Toggles lyrics panel |
| Announce | TTS announcement panel |
| Send Message | Push notification panel |
| Share | Copies track info + a link to your chosen music service (see [Sharing](#sharing)) |
| More Info | Opens AI/Media Info panel |

> The AI entries (AI Search, Vibe, Add Similar Songs, Recommendations, AI Artist Radio) only appear when **Enable AI Features** is on.

### Multi-Room Multicast

- Play the same audio on multiple MA speakers simultaneously
- **Choose Speakers picker** — tap the broadcast icon to start a session: speakers are shown as a scrollable vertical list of pill rows (tap multiple to select for multicast); Play stays pinned at the bottom of the panel so a long speaker list never pushes it out of reach
- **Summary pill** — a single compact pill showing the focused speaker's name and a "+N" count of others playing along with it, instead of a separate pill per speaker
- **Tap to manage** — opens a sheet listing every speaker in the group as the same style of pill row; tap any to focus it for volume control, or tap the × to remove it
- **Sync volumes** — a button in the sheet matches all grouped speakers to the focused speaker's volume
- **Add a speaker** — a "+" button sits next to the summary pill (and solo speakers too, when other MA speakers are available to group with) once something is actually playing
- Long-press menus for individual tracks/albums/etc. always target your current/default speaker directly rather than offering an in-menu speaker picker — use the "+" button to bring extra speakers into a multicast group instead
- Pre-configured Music Assistant player groups (e.g. a permanently-synced stereo pair) can be played on individually, but can't be folded into a separate multi-room session alongside other speakers — this is a Music Assistant limitation, not a card one

### Pinning (Library)

Pin your favourites for one-tap access. All pins also live together in a consolidated **Pinned** section in the Music Library, with its own sub-categories: Songs, Artists, Albums, Playlists, Queues, Radio, Podcasts and Audiobooks.

- **What can be pinned** — radio stations, podcasts, audiobooks, saved queues (see [Pin Queue as Playlist](#pin-queue-as-playlist)), and MA library tracks, artists, albums and playlists
- **How to pin** — long-press any item (or use the pin icon in its info panel) and choose Pin/Unpin; for whatever's currently playing (a song, movie, TV show, or radio station), double-tapping the center of the artwork does the same thing instantly, with a heart-burst animation to confirm it
- **Show Pins in Sections** *(on by default)* — when enabled, pinned items also still appear inline at the top of their own tab (e.g. Pinned Songs in the Songs tab); turn it off in **Caches & Data** so pins only appear in the consolidated Pinned section
- **Management** — view and clear pins individually or all at once from **Caches & Data → Pinned Items** in the visual editor
- Pins are stored on-device only by default and aren't synced between browsers or devices
- Enable **Persistent Pin Storage** in **Caches & Data** to also save pins to Home Assistant's database, so they survive app restarts and cache clears
- Clearing caches (individually or via Clear All Caches) still clears on-device pins — use **Clear Persistent Storage** to also remove the HA-side copy

### Pin Queue as Playlist

Save the current queue as a named snapshot from the queue's 3-dot menu — **Pin Queue as Playlist**. It's saved as a point-in-time copy (not a live link to the original queue) and shows up under **Queues** in the consolidated Pinned section, ready to play back in full any time.

### Music Recap

Open from the quick menu for a personal snapshot of your recent listening.

- **Top Artists & Top Tracks** — up to 10 each, ranked by play count over a rolling last-7-days window
- **AI summary** — a short, warm write-up of your week's listening, regenerated fresh every time the panel opens (not cached, so it always matches the numbers below it). Requires AI features to be enabled — the stats themselves work without AI
- **Tap a track** to open its AI Info panel; **tap an artist** to open their bio — the same panels used throughout the card
- **What counts as a play** — a track has to play past 30 seconds or half its duration (whichever is smaller) to be logged, so quick skips don't pollute your stats
- **What's excluded** — radio streams, podcasts, audiobooks, and system/notification sounds (e.g. announcements) never count toward your Recap
- **Which entities count** — any entity listed in this card's `entities` or `ma_entities` config, not MA-only. Whichever one is actively reporting as playing at a given moment is used, so a speaker with both a native and an MA entity is covered by either
- **Shared across rooms on the same device** — logging is scoped to whatever entities a card is configured with, but the Recap panel itself doesn't filter by entity when displaying results. If you run separate cards per room on the same phone/browser, they all read from one shared history, so every card's Recap shows the combined total of everything any of your cards have logged — not a breakdown per room
- **Catches up on missed plays** — each time a card loads, it also pulls that entity's own state history from Home Assistant (not just what it observes live) to backfill anything played while that card wasn't open — e.g. a different room's tab was active at the time. This needs Home Assistant's recorder to actually be tracking the entity; it looks back up to 48 hours on a card's first-ever load, then only as far as its last check after that
- **Per-user** — if you and other household members use separate Home Assistant accounts, each person's Recap is tracked and stored separately, the same way Pins are
- **Clear History** — a button at the bottom of the panel (with an iOS-style confirmation) permanently deletes your history and resets the history-backfill checkpoint, so cleared plays don't get silently reinstated the next time a card loads; enable **AI Info Persistent Storage** in **Caches & Data** so this history survives app restarts and WKWebView cache evictions. This is the same history [Music History](#music-assistant-library-browser) reads from, so clearing either one clears both

### Video Recap

The same idea as Music Recap, but for movies and TV shows, reachable the same way — a personal snapshot of what's been watched on Apple TV or any other tracked video entity.

- **Top Shows & Top Movies** — ranked by play count over a rolling last-7-days window
- **AI summary** — a fresh, warm write-up of the week's viewing, regenerated every time the panel opens; requires AI features to be enabled — the stats themselves work without AI
- **Tap a title** to open its Media Info panel — poster, synopsis, cast, Similar Movies/Shows, all the same navigation used elsewhere in the card
- **What's excluded** — entries that turn out to just be a bare date (some sources report a recording's date instead of a real title when no title metadata is available) are filtered out automatically, both going forward and retroactively from anything already logged
- **Clear History** — same iOS-style confirmation pattern as Music Recap; enable **AI Info Persistent Storage** in **Caches & Data** so this history survives app restarts. Same history [Watch History](#music-assistant-library-browser) reads from, so clearing either one clears both

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
- **Discogs Panel** — the default panel when AI features are off, and the automatic fallback if AI has no info for a track: same layout with a full tracklist and community rating added; the header reads "Discogs Info" rather than "AI Info" so it's clear where the data came from. Tapping a track in the Discogs tracklist opens that track's own info

**TV Shows:**
- Poster (tap to zoom), genre tags, overview, cast
- Similar Shows — same idea as Similar Tracks; tap any to drill straight into that show's info
- Season and episode browser with formatted airdates
- Full back-navigation from episode detail back to Media Info

**Movies:**
- Poster (tap to zoom), title, year, genre tags, synopsis, cast
- Similar Movies — same idea as Similar Tracks; tap any to drill straight into that movie's info

**Cast navigation:** tap any cast member to open their bio with photo (tap to zoom), Known For credits and a fun fact — the same related-content pattern used throughout the card. Tap their photo in the bio to see a larger version.

**Find Soundtrack:** available from the media player's quick menu while watching on Apple TV, and from the long-press menu on any item in Pinned Movies & TV Shows — opens an AI Search for the film or show's music.

### Queue Panel

- Now Playing row with animated sound bars — tap to open AI Info
- Drag to reorder (MA only, requires Queue Actions)
- Long-press any row: Play Now, Play Next, Move to Top of Queue, Add to Queue, Pin Song, AI Artist Radio, Remove from Queue, Share, More Info
- Queue 3-dot menu: Music Library, Mood, AI Artist Radio, Radio Mode, Announce, Send Message, **Pin Queue as Playlist**, Clear Queue

### Music Assistant Library Browser

Tabs: Recent Searches, Music History, Watch History, Recently Added, Pinned, Favourites, Made for You (Recommended), Playlists, Artists, Albums, Songs, Radio, Podcasts, Audiobooks. (Classic tab-bar mode replaces Recently Added/Pinned with its own MA-native Recently Played queue instead — toggle the layout in **Appearance & Behaviour**. Different feature to Music History: that one is MA's own queue history; Music History below is this card's personal listening history.)

- Tap any item to play; drill into collections with a back button
- Action bar: Play All, Add to Queue, Play Next
- Long-press tracks for the enqueue menu
- **AI Search** — a box at the top of the library for natural-language search; the Songs, Artists and Albums tabs also each have their own AI search button next to the regular search bar, returning matching tracks, artists or albums specifically
- **Recent Searches** — every search you've run, MA and AI alike, most-recent-first, capped at 50; tap to re-run, with an iOS-style Clear confirmation
- **Music History** — a plain chronological list of your last 10 songs played; the 3-dot menu expands the same view to your last 50, or clears history entirely (iOS-style confirmation). A hero bar (Play All / Add All / Play Next) acts on exactly whichever count is currently showing. Tap a song for its AI Info, long-press for the same context menu used throughout the library (Play Now/Next, Add to Queue, Pin, AI Artist Radio, Share). Pinning here lands in the same Pinned → Songs category as pinning anywhere else. This reads from the same history log as [Music Recap](#music-recap) — just as a list instead of weekly stats — so clearing history from either one clears it for both
- **Watch History** — the movie/TV equivalent, same shape: last 10 (expandable to 50), 3-dot menu with Clear History, long-press for Pin/Find Soundtrack, reads from the same history as [Video Recap](#video-recap). No hero bar here — replaying several movies/shows back-to-back the way a music queue does isn't really the natural action for video the way it is for songs
- **Podcasts tab** — search iTunes directly; pin favourites
- **Audiobooks tab** — search free, public-domain titles on LibriVox via the Archive.org catalogue, with AI-assisted query refinement and chapter-by-chapter playback; pin favourites
- **Radio tab** — search radio-browser.info directly, or use **Browse Home Assistant Radio** to explore categories from HA's own Radio Browser integration (requires the [Radio Browser](https://www.home-assistant.io/integrations/radio_browser/) integration under **Settings → Devices & Services**)
- **Pinned tab** — everything you've pinned, grouped into Songs, Artists, Albums, Playlists, Queues, Radio, Podcasts and Audiobooks
- **Remembers where you left off** — reopening the library returns to whichever tab you were last in, even after fully closing and reopening the app (within a few hours; a deliberate close resets it back to the top)

### Apple TV Remote

| Button | Action |
|--------|--------|
| **Back** | Navigate back / menu |
| **TV** | Home screen / wakes from sleep |
| **Power Off** | Sends `suspend` to sleep the Apple TV |

**Keyboard Panel** — the moment an Apple TV's on-screen keyboard becomes active (searching in an app, entering a password, etc.), a text input automatically appears over the card. Type on your phone's own keyboard and tap Send to push the text straight to the TV, instead of the actual remote's slow letter-by-letter D-pad entry. Detected via Home Assistant's own entity registry (finds whichever `binary_sensor` shares the same device as your Apple TV entity, so it works regardless of how entities happen to be named) — no setup needed beyond having the Apple TV integration configured. Toggle in **Appearance & Behaviour**.

---

## 🔧 Visual Editor

The editor includes a filter box at the top (search any setting by name) and a **Reset All Settings to Defaults** button. Several sections are collapsible — tap the header to expand.

| Section | Settings |
|---------|---------|
| Manage & Reorder Media Players | Drag-and-drop reorder; enable/disable; tap a speaker's name to open its own dedicated settings page (Display Name, Startup Volume, Volume Entity, MA Speaker toggle) |
| Appearance & Behaviour *(collapsible)* | Follow HA Theme, Auto Switch, Remember Last Speaker, Media Player Selector, Music Library Layout (iOS-style category list vs. classic tab bar), Always Show Library Button, Show Remote Button, Apple TV Keyboard Panel, Default Radio Mode on Startup, iTunes Artwork Fallback, Show Volume HUD, Live/Podcast/Audiobook Pill, Show YouTube Button, Volume Buttons, Volume Percentage, Scroll Long Text, Lyrics persistence and caching, and a **Startup & Navigation** sub-section (Startup View, Retain Current View, Remote Button Row Position) |
| Caches & Data *(collapsible)* | AI caches (bios, trivia, where-to-watch, content warnings, year-in-music, vibe history, AI response cache), artwork caches (iTunes, Wikipedia) with Persistent Storage toggles, library & radio caches (MA library, radio stations, HA registry), lyrics cache & scroll style with its own Persistent Storage toggle, and management of all pinned items including a **Show Pins in Sections** toggle, a Persistent Pin Storage toggle and a Clear Persistent Storage button |
| Visual Effects | Card Liquid Glass, Remote Liquid Glass, Volume HUD Liquid Glass, Ambient Glow, Library & Queue Row Glow, Artwork Crossfade, Pin Hearts, Resize Button Spin |
| ✨ AI Settings | **Enable AI Features** master switch (off by default), AI Agent selector, Share Track service (YouTube Music, Apple Music, Spotify, Tidal, Amazon Music, Deezer), Announce TTS Service, Song Intro toggle |
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
ai_features_enabled: true
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

> **Note:** `ma_entities` should list your MA speaker entities (e.g. `media_player.mass_kitchen_homepod`). These do **not** need to also appear in `entities`. AI features are **off by default** — the example above enables them; leave `ai_features_enabled` out (or set it `false`) for a Discogs-powered card with no AI.

---

## 🔧 Troubleshooting

**AI features show "Could not build queue" or "AI rate limit reached"**
- Your Gemini daily quota (1,500 requests for `gemini-2.0-flash`) is exhausted. It resets daily.

**AI features are missing from the quick menu, or don't work**
- Check **Enable AI Features** is turned on in the editor's **AI Settings** — AI is off by default, and its quick-menu entries are hidden until it's enabled.
- Ensure the **Generative Language API** is enabled in Google Cloud Console — this is the most common setup mistake.
- Confirm **Google AI Conversation** is selected as the AI Agent in the visual editor.

**Vibe queue plays only one song or none**
- Ensure your MA speaker entity has the **Music Assistant Speaker** toggle enabled in the visual editor.
- The card needs the `media_player.mass_*` entity, not the native HomePod or Apple TV entity.

**Summary pill stays greyed out after a queue builds**
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
