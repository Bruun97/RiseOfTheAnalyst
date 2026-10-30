# Rise of the Analyst

A pixel-art RPG for the phone. You are Dave. You start unemployed in a bedsit above a chip shop in the village of Ashbury, with a finance degree, $1,500 and a train line into Lombard City. Thirteen financial firms are hiring, from a prop shop above a kebab house to the world's biggest sovereign fund. Apply, interview, trade, climb, switch, get fired, try again. Finance is the "combat": every trade is a bet on a hidden signal you only ever see through noisy sources.

Single HTML file, no build step, no backend, no accounts. Installable as a PWA. English UI.

All firms, people, places and prices are fictional. Some firms are affectionate parodies of institutions you may recognise.

## Play

- **Touch:** tap-only. Tap anywhere to walk there. Tap a person, a desk, a door or a whole building to walk over and use it. Tap the train to go to the platform. No d-pad.
- **Desktop:** arrows / WASD to move, Enter or Space to interact, Esc to close menus.
- **A day** is 8 energy (more with a better flat). Research costs 2, lessons and favours cost 1, coffee and food give 1 back. Sleep in your bed, or take the lift and the train home, to end the day. The market moves overnight.
- **Money:** rent is due every Monday. Fall two weeks behind and you are evicted. The train costs $6 each way unless you buy a season ticket. Unemployment benefit is $60 a day, which does not cover rent.
- **One employer at a time.** While employed, every other firm shows RESIGN FIRST. Resign from the laptop, or at another firm's reception as part of applying. Resigning keeps your reputation and CV but drops your book and positions. Fail that interview and you are unemployed.
- **Jobs:** every firm's door is a reception desk. It shows what the firm pays, how big the book can get, how fast they fire and what they require. Qualify, and you get a three-question interview scored on your stats. Big firms pay less to start and promote slowly, but their ceiling is enormous. Hedge funds pay more, pay bonuses, and fire at the first bad quarter.
- **Career:** every 12 working days there is a quarterly review against your rival. Beat them and the index to build firm reputation, which earns promotions. Your global reputation and your CV carry between firms; your colleagues do not.
- **Certificates:** study on the laptop or in the library, then sit the AFA exam at the institute in the city. Level I opens the banks. Level II opens Silverman Sachs and the sovereign fund.
- **Win:** reach the top rank at a firm whose book can exceed $100M, or found your own fund once you have the money and the name.
- **MENU** shows stats, career, personal portfolio, quests and relationships.

## The firms

| Firm | Type | Start book → ceiling | Fired at | Needs |
| --- | --- | --- | --- | --- |
| Brickhouse Trading | prop shop | $300k → $3M | −25% | nothing |
| Barkleys Bank | high-street bank | $1M → $25M | −8% | Charm 2 |
| German Bank | investment bank | $3M → $100M | −10% | AFA I, Analysis 3 |
| HBBB Bank | global bank | $2M → $120M | −8% | AFA I, Charm 3 |
| Silverman Sachs | bulge bracket | $5M → $300M | −10% | AFA II, Reputation 40, Analysis 4 |
| BlackBoulder | asset manager | $20M → $1B | −6% | AFA I, Reputation 25 |
| Rearguard Investments | passive manager | $10M → $500M | −4% | Charm 2. No shorting, 20% max positions |
| North Sea Sovereign Fund | sovereign wealth | $50M → $2B | −5% | AFA II, Reputation 50. No shorting, no bonus, energy stocks excluded |
| Riverbridge Associates | macro hedge fund | $10M → $150M | −15% | Reputation 35, Nerve 4 |
| Reformation Technologies | quant fund | $20M → $200M | −12% | Analysis 6 |
| Bastion Capital | multi-strategy pod shop | $8M → $120M | −7% | Reputation 30, Nerve 3. Book halved at −4% |
| Hampshire Hathaway | value investor | $5M → $400M | −30% | Analysis 4, Charm 3. No shorting |
| Halloran Capital | boutique hedge fund | $2M → $150M | −12% | Reputation 15 |

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole game: CSS, maps, sprites, firms, dialogue, market sim, save/load. ~100 KB. |
| `manifest.json` | PWA manifest: name, standalone display, portrait lock, icons. |
| `sw.js` | Service worker. Own files network-first with cache fallback (works offline after first visit); fonts cache-first. Bump `CACHE` when you ship a new version. |
| `icon-180.png` | iOS home-screen icon. |
| `icon-192.png`, `icon-512.png`, `icon-512-maskable.png` | Android / Chrome icons. |

## Deploy

Put the folder contents in the root of a GitHub Pages repo (Settings → Pages → deploy from branch), or drop the folder on netlify.com/drop. Paths in `manifest.json` and `sw.js` are relative, so a project page (`user.github.io/repo/`) works as well as a root domain.

On iPhone: open in Safari → Share → Add to Home Screen. Runs full screen without browser chrome. Safari-tab storage and home-screen-app storage are separate, so a save made in the tab does not carry over to the installed app.

Saves live in `localStorage` under the key `rota.v2`. Nothing leaves the device.

## How `index.html` is organised

Everything lives in one `<script>` IIFE. Sections, in order:

1. **Constants and helpers** – `T` (16 px tile), formatters, RNG, `hash()`.
2. **Sprites** – `sprite(rows, palette)` turns ASCII art into a canvas. `charFrames(hair, shirt, pants, skin)` builds a 12×16 character in 4 directions × 2 frames plus a portrait. `makeTiles()` builds the tile set for office, home and outdoors. No image assets anywhere.
3. **Market** – `TICK`: eight fictional stocks. Each has a hidden `drift` (the true signal) and volatility; prices move overnight as `drift + vol·noise`.
4. **FIRMS** – the thirteen employers. Each has salary, starting book, ceiling, firing threshold, bonus share, promotion speed, interview difficulty, the stats it values, hard requirements, optional house rules (`noShort`, `exclude`, `maxSize`, `halveAt`), a four-person staff (boss, mentor, rival, extra) and a few firm-specific lines.
5. **INTERVIEWS** – three questions per firm type. Each answer leans on a stat; the firm's `likes` decides how much it counts.
6. **Maps** – `OFFICE_MAP` and `HOME_MAP` are ASCII grids. `mkCity()` and `mkVillage()` generate the outdoor grids and a list of buildings; buildings are drawn procedurally with a sign, windows and a door. Legend: `#` wall, `W` window, `G` glass, `D`/`d`/`P` desks, `K` kitchen, `C` coffee, `T` terminal, `E` lift or door, `b` shelf, `p` plant, `z` bed, `l` laptop, `F` fridge, `g` grass, `t` tree, `f` flowers, `S` pavement, `R`/`M` road, `~` water, `q` quay, `-` rails, `=` platform, `B` building, `O` building door.
7. **State `S`** – everything that is saved: day, energy, cash, stats, `job` (firm, rank, firm reputation, quarter P&L, rival P&L, days), firm positions `pos`, personal holdings `ppos`, prices, home tier, study points and certificates, cooldowns, CV, the active earnings case `ms`, the insider flag, the current scene and player position.
8. **Quests** – `QUESTS` is derived from state, so quests never desync.
9. **Scenes** – `buildScene()` returns the active map; `spawnNPCs()` builds the firm's staff from `FIRMS`; `go()` switches scene.
10. **Dialogue** – `say(npc, text, choices)` with typewriter text. `DIALOG.boss/mentor/rival/extra` are generic over firms. The earnings case (`startMission`, `clueText`) and the insider tip live here.
11. **Objects and places** – `interactTile()` for office and home furniture, `doorTrigger()` for buildings, `reception()` and `interview()`, café, pub, library, AFA institute, and the laptop (job portal, study, personal brokerage, housing, season ticket, founding your own fund).
12. **Desk** – `deskMenu()` and the research minigame `openResearch()`, which enforce house rules.
13. **Day cycle** – `endDay()` moves prices, marks positions, pays salary or benefit, collects rent, resolves the earnings event, checks eviction, the regulator and firing, and triggers `quarter()` every 12 working days.
14. **Menu sheet, HUD, input, render loop, boot.**

`window.__dj` exposes `S`, `P`, `go`, `hire`, `endDay`, `quarter` and more for debugging and automated tests.

## The earnings case

Once per firm, from day 2, the boss hands you a stock that reports in three days. The outcome (beat/miss) is rolled secretly. Five clues of varying reliability: the mentor (fundamentals, always right), the extra colleague (data, insider filings or kitchen gossip depending on who they are), the terminal (tape), and the café in the city (a sell-side analyst on the phone, right about two thirds of the time). Decide at your desk: long, short, stay out, or pitch the boss for firm capital (Nerve 3, doubles the stake). The stake is sized to how fast the firm fires. Six different debriefs depending on direction, size and honesty.

## Adding content

- **New firm:** add an entry to `FIRMS` and a slot in `mkCity()`. Everything else (reception, interview, office, staff, mission) is generic.
- **New interview style:** add a key to `INTERVIEWS` and use it as a firm `type`.
- **New building in town:** add to the `slots` in `mkCity()` or to `B` in `mkVillage()`, then handle its `kind` in `doorTrigger()`.
- **New stock:** add to `TICK`.
- **Balance knobs:** energy per day (`HOMES`), research cost (2), position sizes (15/35/60%), quarter length (12 days), rent, fare, benefit ($60), exam thresholds (4 and 10 study points), rival drift in `endDay()`.

## Roadmap ideas

Market-wide crash days, a client-dinner mission for the banks, a compliance investigation mission, NPCs on the street, save export/import, a leaderboard of quickest routes to Partner.

## Credits

Fonts: Silkscreen and IBM Plex Sans via Google Fonts. Everything else is drawn in code. All companies, people and prices are fictional.
