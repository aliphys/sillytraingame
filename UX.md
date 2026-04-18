# UX.md — Hoop Trains

**One-line:** A one-button co-op game for strangers on the same Skånetrafiken train.

**Demo context:** Lund hackathon, Sunday afternoon, April 2026. Big projector + white canvas screen. People scan a QR code and join on their phone. 20 hours to build.

**Narrative arc:**
- **Today:** Hoop Trains — the game. Every rider draws a line on a shared Skåne map.
- **Tomorrow:** Capsule — every line remembers. Riders leave songs, clips, notes behind for future strangers.

---

## 1. Voice & Tone

Sparse. Rhythmic. Short sentences. Lowercase where it reads warmer; bold headline + muted subtitle.

**Good:**
> You're on the train.
> Lund. Malmö. Hyllie. Ystad.

**Bad:**
> Welcome to Hoop Trains, the innovative multiplayer experience that connects commuters across the Skåne region.

**Copy rules:**
- Max 6 words per headline.
- Max 10 words per supporting line.
- Never say "platform," "solution," "experience," "leverage," "ecosystem."
- Never use emoji except 🚆 and only in the loading screen.
- Never explain the game in the game. Show it by playing it.

---

## 2. Design System

### Colors

| Token | Hex | Usage |
|---|---|---|
| `--bg` | `#FFFFFF` | Page background |
| `--ink` | `#111111` | Primary text, outlines |
| `--muted` | `#6B6B6B` | Secondary text |
| `--faint` | `#C8C8C8` | Dividers, hairlines |
| `--accent` | `#C8102E` | YOU marker, the single red moment |
| `--paga` | `#645FA2` | Pågatåg (primary line color) |
| `--oresund` | `#8A8F97` | Öresundståg silver |
| `--bus` | `#FEC900` | SkåneExpressen yellow |
| `--flex` | `#0099D8` | Skåne Flex blue (dashed) |
| `--city` | `#2E9B4E` | City buses green (reserved, unused in MVP) |

**Rule:** 1 accent (red) + neutrals + the Skånetrafiken line family. Everything else is black, white, or grey.

### Typography

- **Display:** Inter (or DM Sans) Bold, 30–48px, tight letter-spacing, left-aligned.
- **Body:** Inter Regular, 14–18px, `#6B6B6B`.
- **Never center body text.** Never justify.
- **Line-height:** 1.2 for display, 1.5 for body.
- **Tabular-nums** on timers, scores, rider counts.

### Layout

- Desktop/projector: split 50/50. Copy left, Skåne diagram right.
- Phone (controller): single column, big tap target, nothing else on screen.
- 8pt spacing grid. Gutters 48px desktop, 16px mobile.

### Motion

- Entrances: 400ms ease-out, fade + 8px up.
- Line-drawing: 800ms per segment, stroke-dasharray animation.
- Tap feedback: 100ms scale to 0.96, bounce back.
- Respect `prefers-reduced-motion: reduce` — disable all non-essential motion.

---

## 3. Game Rules

- **Input:** one button (tap anywhere on phone screen, or keyboard `SPACE`).
- **Objective:** stay on your line. Tap to hop over oncoming hoops / obstacles. Reach the next station. Collect stars along the way.
- **Win condition:** carriage reaches the "middle" (a shared destination like Lund C) together. Team score = sum of stars.
- **Lose condition:** none in MVP. Crashing = respawn in 2s. No game-over screen.
- **Round length:** 90 seconds.
- **Players per round:** 2–8 riders on one phone each, all watching the shared projector screen.

---

## 4. Screens

Four screens total. Numbered flow.

### 4.1 Lobby Screen (projector)

**Purpose:** People in the room scan a QR code and join.

**Layout:**
- Top-left: `HOOP TRAINS · LUND · APRIL 2026` (muted, 10pt, bold).
- Headline (left, 40%): **"You're on the train."** 48pt bold.
- Subtitle: "Scan the code. Pick a line. Tap to hop." 20pt muted.
- Right side: large QR code (min 400×400px), cream background, black ink, clean quiet zone.
- Below QR: room code in monospace, 32pt — e.g. `LUND-7823`.
- Bottom-left: live roster appearing as riders join — "P1 · P2 · P3…" in colored roundels matching their assigned line.

**States:**
- 0 riders: "Waiting for the first rider."
- 1 rider: "One rider. Tap to begin. Or wait for more."
- 2+ riders: big button → "START (in 10s)" with countdown. Host (projector) or any rider can confirm early.

**Copy:** Do not explain the game here. Rule: if someone has to read instructions to play, we failed.

### 4.2 Controller Screen (phone)

**Purpose:** The player's only screen during the round.

**Layout (full-screen, portrait):**
- Top: your line color bar, full-width, 12px tall.
- Top-center: your rider tag (e.g. "P3") in a roundel matching your line color.
- Middle: the entire screen is one tap target. No buttons. No text.
- Bottom-center (muted 14pt): "Tap to hop." — disappears after first tap.
- Bottom-right (tabular): round timer `01:23` → `00:00`.
- Bottom-left (tabular): your stars collected `★ 3`.

**Haptics:**
- Every tap → 10ms light haptic.
- Collision → 40ms heavy haptic.
- Star collected → 20ms medium haptic.
- Round end → 80ms double pulse.

**Orientation:** lock portrait. If phone rotates, show a "Please hold your phone upright" overlay.

**Connection loss:** phone shows a greyscale version of the screen + "Reconnecting…" in the top. Game continues on projector; auto-rejoin on reconnect.

### 4.3 Projector Screen (shared display, during play)

**Purpose:** the one everyone in the carriage watches.

**Layout:**
- Background: the Skåne schematic map from the one-pager — Pågatåg purple, Öresundståg grey, etc. But dimmed to 35% opacity so the game reads on top.
- Each player is a small rider roundel (P1–P8) traveling along their assigned line.
- Stars scattered along the track — collected stars fade out; uncollected stars pulse gently.
- Top-center: round timer, large, tabular.
- Top-right: collective score "★ 14" — big red accent when it updates.
- Bottom-left legend: only the lines currently in play (not all 9).
- No text instructions. No HUD clutter.

**Camera:**
- Zoomed in around the action. Follows the centroid of active players.
- Smooth pan, not snap — 400ms ease.

**When a rider reaches a station:** a soft ping chime + station name briefly appears as a cream label callout near their roundel (matches one-pager style).

### 4.4 Result Screen (projector + phone)

**Purpose:** the "we made it" moment. 10 seconds, then loops back to lobby.

**Projector layout:**
- Full-screen, white.
- Big headline, left-aligned: **"We made it."** 56pt bold.
- Subtitle: "8 riders. 1 carriage. 14 stars." (muted, 22pt)
- Below: the Skåne map reveals the full trail of every rider's line drawn in their color, animated stroke-dasharray left-to-right over 2 seconds.
- Bottom-left: small text "Play again — same code. LUND-7823."

**Phone layout:**
- Full-screen, same color as your line.
- Large white text: **"★ 4"** (your stars).
- Below: "We made it."
- Below: muted "Next round in 8s."

**No leaderboards. No podiums. No scores-to-beat.** The point is shared effort, not competition.

---

## 5. Interaction Rules

- **One button.** No menus during play. No pause. No settings mid-game.
- **No accounts.** No login. No email. No signup. Phone joins via QR → plays → leaves. Room state is ephemeral.
- **No tutorial.** The first round IS the tutorial. If someone crashes, they respawn and try again.
- **Plan B — keyboard fallback:** press `K` on the host laptop to toggle a keyboard-per-player input mode. Players use rows of keys (`A` / `L` / `Z` / `M` etc.) as their hop button if phone connectivity fails at the demo.
- **Plan C — solo keyboard demo:** original MVP two-player A/L keyboard game boots if the WebSocket server is down. 30-second backup reel plays if that too fails.

---

## 6. Accessibility

- **WCAG AA contrast minimum.** Black `#111` on white `#FFF` is 17.4:1. Muted `#6B6B6B` on white is 5.7:1 (AA for body).
- **Never rely on color alone.** Every line has a letter code (P1…P9) in addition to its color.
- **Reduced motion:** when `prefers-reduced-motion` is set, line-drawing animations become instant, entrance fades drop to 100ms, haptics remain.
- **Font floor:** 14px. No body text smaller.
- **Touch targets:** 64×64px minimum on phone. Whole screen is the target anyway.
- **Screen reader:** controller exposes "You are rider P3. Tap to hop." as an aria-live region. Projector is decorative (no screen-reader audience).

---

## 7. Technical UX Constraints

- **Controller weight:** <80KB initial HTML+JS+CSS. Must load on a train with 3G in 2 seconds.
- **Projector latency:** <150ms from tap on phone → hop on projector. Aim for 80ms.
- **Frame rate:** 60fps on projector. Phone is static UI, no frame target.
- **Offline:** if phone drops connection, controller greys out but remains tappable — queued taps flush on reconnect.
- **WebSocket rooms:** max 8 players per room, enforced client and server side.
- **QR code:** must contain a direct URL with room code embedded, e.g. `https://hooptrains.app/c/LUND-7823`. No redirects.
- **Phone wake lock:** request wake lock when controller loads so the screen doesn't dim mid-round.

---

## 8. Capsule Vision (post-demo, not MVP)

Shown on one slide, not built:

- At the end of each round, riders can drop one thing into the carriage's capsule — a Spotify link, a YouTube clip, a single sentence.
- The next rider on that route opens the capsule and finds what the last one left.
- No names. No feed. No likes. Just the trace.
- Tomorrow: the bus. The café. The festival. Every place people share an hour without speaking gets a capsule.

**UX cue in this MVP:** on the Result Screen, a faint ghost text reads "Every line remembers." That's it. The full feature ships later.

---

## 9. File & Route Structure

```
/                       → marketing one-pager (future)
/c/:roomCode            → controller (phone)
/p/:roomCode            → projector (host)
/new                    → create new room, returns roomCode + QR
```

**Component tree sketch:**

```
<App>
  <LobbyScreen /> when state=lobby
  <ControllerScreen /> on /c/*
  <ProjectorScreen /> on /p/*
    <SkaneMap />
    <RiderLayer />
    <HUD />
  <ResultScreen /> when state=result
```

---

## 10. Reference Assets

In the repo `/docs/` when shipped:
- `one-pager.png` — the Skånetrafiken one-pager for the pitch
- `pitch-deck.pptx` — 9-slide deck
- `UX.md` — this file

**Keep it small. Keep it quiet. Keep it one button.**
