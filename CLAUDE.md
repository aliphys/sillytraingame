# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development

There is no build step. The game is a single self-contained HTML file served as a static site.

In production, Vercel serves `public/` as a static site (`vercel.json` sets `outputDirectory: "public"` with no build command).

## Architecture

**All active game code lives in `public/index.html`** — a single ~775-line file with embedded CSS and JavaScript. No bundler, no modules, no transpilation.

### Multiplayer model

The game uses [PeerJS](https://peerjs.com/) (loaded from CDN) for WebRTC peer-to-peer networking. Players share 6-character room codes for matchmaking. There are three roles determined at connection time:

| Role | `isHost` | `isServer` | `myIdx` | Description |
|---|---|---|---|---|
| Dedicated host | `true` | `true` | `null` | Runs game loop, doesn't play |
| P1 host | `true` | `false` | `0` | Runs game loop AND plays as P1 |
| Remote player | `false` | `false` | `0\|1` | Receives state, sends inputs |

### Game loop & state sync

- The host (dedicated or P1) runs `tick()` every `TICK_MS = 33ms` (~30 FPS)
- Each tick computes physics, star collection, and win detection, then broadcasts the full `gameState` object to all connected peers
- Remote players only render; they never compute physics

**Host → Players:** `{ type: 'state', state: gameState }` — sent every tick  
**Host → Player on connect:** `{ type: 'assigned', idx: 0|1 }`  
**Player → Host:** `{ type: 'switch' }` — when the player presses their key/button  
**Player → Host:** `{ type: 'restart' }` — when requesting a rematch  

### Game mechanics

- Canvas: 680×420px, scales responsively via CSS transform
- 3 circular hoops arranged horizontally; players ride around them
- Switching hoops is the only player action (P1: `A` key; P2: `L` key; both have on-screen touch buttons)
- Stars spawn randomly on hoops and respawn 1.8s after collection
- `WIN_SCORE = 5` — first player to collect 5 stars wins

### UI screens

Four `<div class="screen">` elements toggled via the `.active` class:
1. **Lobby** — mode selection (Host / Create as P1 / Join with code)
2. **Waiting** — displays room code and connection status
3. **Game** — score bar, canvas, switch button
4. **Results** — winner announcement and "PLAY AGAIN"

Physics is normalized to 60 FPS internally (`dt`-based movement) so `TICK_MS` changes don't affect game speed.
