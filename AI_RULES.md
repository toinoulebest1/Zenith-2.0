# AI Rules & Tech Stack Documentation

## Tech Stack

*   **Frontend:** Vanilla HTML5, CSS3, and JavaScript (ES6+). No frontend framework (React/Vue/Angular) is currently used.
*   **Backend:** Python 3 using **Flask**.
*   **Deployment:** Configured for Vercel Serverless Functions (`vercel.json`, `api/index.py`).
*   **Styling:** Custom CSS (`magic.css`, `style.css`) using CSS Variables for theming.
*   **Icons:** FontAwesome (via CDN).
*   **Audio Engine:** HTML5 `Audio` API with `AudioContext` for effects (8D, EQ, Karaoke).
*   **Casting:** Google Cast SDK (`cast_sender.js`).

## Development Rules & Conventions

### 1. Backend (Python/Flask)
*   **Structure:** The core entry point for Vercel is `api/index.py`. Local development uses `server.py`.
*   **API Calls:** Use the `requests` library for all external HTTP calls (Qobuz, Tidal, Deezer, etc.).
*   **Modules:**
    *   `qobuz_api.py`: Handles Qobuz authentication and stream URL retrieval.
    *   `lyrics_search.py`: Handles lyrics retrieval from LRCLib.
*   **Response Format:** All API endpoints must return JSON. Errors should return appropriate HTTP status codes (404, 500).

### 2. Frontend (HTML/JS)
*   **DOM Manipulation:** Use standard API (`document.getElementById`, `querySelector`). Do not introduce jQuery or other DOM libraries.
*   **State Management:** Global variables (e.g., `window.tracks`, `window.audio`) are currently used for player state. Keep this pattern unless a refactor is requested.
*   **Audio:**
    *   Use `crossOrigin = "anonymous"` for audio elements to allow `AudioContext` processing.
    *   Effects (EQ, Panning) must handle `AudioContext` states (suspended/running).
*   **Modularity:** Keep distinct features in separate files (e.g., `blindtest.js`, `cast.js`) and include them in `index.html`.

### 3. External Integrations
*   **Tidal (source principale):** Eljohnzc addon (`ELJOHNZC_BASE`). `/search?q=` returns tracks with Tidal IDs; `/stream/{id}?quality=` returns a DASH manifest URL. The backend serves it through `/tidal_manifest/{id}` (Shaka) and segments go through `/tidal_proxy`. Tracks use `source: 'tidal_hund'`.
*   **Qobuz:** Paused (`QOBUZ_ENABLED=0`). Requires valid `app_id`, `user_id`, and `token` when re-enabled. Use `qobuz_api.py` wrapper.
*   **Lyrics:** Prioritize synced lyrics from LRCLib.
*   **Images:** Tidal images are constructed via UUID; Qobuz images via URL replacement (`_600` vs `_300`).

### 4. Styling
*   **Tokens:** All colors, radii and fonts are CSS variables at the top of `style.css`. Use `var(--primary)` / `rgba(var(--primary-rgb), a)` for the accent (user-selectable in Settings, stored in `localStorage.zenith_accent`) and `var(--primary-ink)` for text on accent backgrounds. Never hardcode the accent color.
*   **Fonts:** `--font-display` (Bricolage Grotesque) for headings, `--font-ui` (Manrope) for everything else.
*   **Layout:** `.app-layout` is a grid (sidebar / main / "now playing" panel, player bar across the bottom). ≤1180px: icon-only sidebar. ≤1024px: the now-playing panel becomes a full-screen view (`body.np-open`). ≤768px: bottom tab bar (`.mobile-tabbar`), the sidebar becomes the "Plus" sheet, mini-player above the tabs.
*   **Views:** Switch views with `hideAllViews()` then show the target; set the menu state with `setActiveNav(key)`. Use `uiState('loading'|'empty'|'error', msg)` for loading/empty/error states instead of inline markup.
*   **Cards:** Wrap covers in `.card-media` (badges, `.card-play` button and the playing indicator live inside it).
*   **Effects:** Easter eggs and visual modes are handled in `magic.css`.