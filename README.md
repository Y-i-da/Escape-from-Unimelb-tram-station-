# 🚊 Escape from UniMelb Tram Station

> *A pixel-art survival game where a broke UniMelb student dodges ticket inspectors — and when caught, argues their way out of a $226 fine with the help of Claude AI.*

![Demo](demo_reel.html)

---

## 🎮 Play

### Option A — Standalone (no LLM)
Just open `tram_dodger.html` in any modern browser. Full game works without a server; the interrogation scene falls back to a default verdict if `/api/interrogate` is unreachable.

### Option B — Full LLM mode
```bash
# 1. Install dependencies
npm install

# 2. Set your Anthropic API key
export ANTHROPIC_API_KEY=sk-ant-...

# 3. Start the server
node server.js

# 4. Open in browser
open http://localhost:3000
```

---

## 🕹️ Controls

| Key | Action |
|-----|--------|
| `↑↓←→` / `WASD` | Move |
| `SPACE` | Disguise — blend into the crowd for 5 s |
| `Q` | Due Deadline Rage — push NPCs aside (if selected) |
| `M` | Mute / unmute BGM |
| `R` | Restart (after game over) |
| `1` / `2` | Select skill before each round |

---

## 🧠 How It Works

### Classical AI — A\* Pathfinding
Inspectors navigate the tile grid using a custom A\* implementation. Door states (open/closed) are baked into the walkability function so inspectors path through open tram doors but stop at closed ones.

During **Disguise** skill, A\* is replaced by random-walk wander — inspectors display a flashing `?` and meander aimlessly until the disguise expires.

### Generative AI — Claude Interrogation
When the player is caught without a Myki Card, instead of an instant game-over the game transitions to a **live interrogation**:

```
Player caught → Inspector questions → Player types excuse
     → POST /api/interrogate → Claude Haiku evaluates
     → { verdict: "released" | "fined" | "arrested",
         message: "...", reason: "..." }
     → Verdict applied: resume game OR game over
```

The LLM call is fully async — the game loop never blocks. JSON is extracted with a regex fallback so the game degrades gracefully even if the model adds surrounding text.

---

## 🏗️ Architecture

```
tram_dodger.html   ← entire game (Canvas 2D, Web Audio API, A*)
server.js          ← Express proxy to Anthropic API
package.json       ← dependencies (express only)
demo_reel.html     ← standalone 12-second cinematic demo (with BGM)
```

---

## 🎵 BGM

Generated entirely through the **Web Audio API** — square and sawtooth oscillators, gain envelopes, and a `setTimeout`-based scheduler. Zero audio files, zero CDN dependencies.

---

## 🗺️ Game Map

```
Col  0-4   Platform  (player zone, inspector patrol)
Col  5     Tram wall / sliding doors
Col  6-7   Tram interior  (passengers, hiding spots)
Col  8     Far tram wall
Col  9-10  Train tracks  (out of bounds)
```

22 rows × 11 columns @ 36 px per tile = **396 × 792 px** canvas.

---

## 📦 Tech Stack

- HTML5 Canvas 2D API
- Web Audio API (procedural chiptune BGM)
- Vanilla JavaScript (ES2022)
- A\* pathfinding (custom, ~40 lines)
- Node.js + Express.js
- Anthropic Claude API (`claude-haiku-4-5-20251001`)

---

## 🙏 Inspiration

Swanston Street stop sits one stop outside Melbourne's Free Tram Zone, right at the front gate of the University of Melbourne. It is one of the most aggressively inspected stops in the city. We've both been caught there. The $226 fine stings less than the principle of it.

*One sleepless night + one too many infringement notices = this game.*

---

## 👥 Authors

**Ellitt** and **Nannan** — UniMelb students, fare evaders (reformed), game developers (aspiring).

---

## 📄 License

MIT — see [LICENSE](LICENSE).
