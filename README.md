# Rise of the Analyst

A pixel-art RPG for the phone. You are Dave. You have a finance degree, $400, a room above a chip shop at the wrong end of Ashbury, and a job: office junior in the post room of BlackBoulder, the biggest asset manager on Earth. Every morning it is the bus to Millbrook, the stopping train to Brayford Junction and the express into Lombard City, a big river city of glass towers and stone banks. Carry the post, make the coffee, print the morning pack, and read everything you can get your hands on. Reading throws up insights; pitch them to the right person; when the tape proves you right, trust grows. Enough trust and knowledge and you get a desk, a login and a $20M book. Then the real climb begins: Analyst, Senior Analyst, Portfolio Manager, Managing Director.

Single HTML file, no build step, no backend, no accounts. Installable as a PWA. English UI.

All firms, people, places and prices are fictional. Some firms are affectionate parodies of institutions you may recognise.

## Play

- **Touch:** a round stick bottom-left: drag it to walk. The yellow ACT button bottom-right talks to whoever you face, uses the thing in front of you, and confirms a one-button dialog. Tapping still works too: tap a tile to walk there, tap a person, a desk, a door or a whole building to walk over and use it, tap the rails to go to the platform. Pinch, or use the + / − buttons, to zoom; the level is remembered on the device.
- **Desktop:** arrows / WASD to move, Enter or Space to interact, Esc to close menus. Mouse wheel zooms.
- **A day** is 8 energy (more with a better flat). Duties, reading and lessons cost 1, research costs 2, coffee and food give 1 back. Sleep in your bed, or take the lift and commute home, to end the day. The market moves overnight.
- **The commute:** Ashbury bus stop → Millbrook station → Brayford Junction platform 4 → Lombard Central. Bus $2, train $4, express $6: $12 each way. Pick the final destination at any stop and the game plays the changes for you (a short fade with the train's name between towns); or get off at an intermediate town and look around. Choosing Wait steps you back off the platform. Read on the train for 1 energy and a knowledge point. A season ticket ($220, 30 days) covers bus, trains and the tube. Moving house towards the city shortens the commute.
- **Money:** rent is due every Monday. Fall two weeks behind and you are evicted. The junior's salary is $95 a day; after commute and rent there is not much left. The Financial Ledger costs $3 and is worth it.
- **The junior loop:** post round, printer, coffee round, each once a day, each +2 trust. The shelf, the research notes on Marcus's spare chair, the paper on the train, the library in Millbrook and your laptop all raise one of six knowledge topics (Accounting, Valuation, Macro, Markets, Risk, Sectors, 0–10 each). Reading sometimes produces an **insight**: a directional call on a stock. Pitch it to Susan (safe) or Lawrence (bigger swing). Three days later the tape decides: right calls add trust and reputation, wrong ones cost a little. The terminal is off limits until trust 25.
- **Promotion to Analyst:** trust 40, knowledge 10, two correct calls. Lawrence hands you the yellow desk and a $20M book.
- **Analyst and up:** research at your desk (your read on the hidden signal gets clearer with Analysis and knowledge), take positions, survive the quarterly review against Marcus every 12 working days. Firm reputation earns promotions: Senior Analyst needs AFA level I, Portfolio Manager needs AFA level II. Lose 6% of the book in a quarter, or lose the review badly, and you are back in the post room with your trust reset.
- **The earnings case:** once you are an Analyst, Lawrence hands you a stock that reports in three days. Five clues of varying reliability; decide at your desk; six different debriefs.
- **Win:** Managing Director at BlackBoulder, or found your own fund once you have $300k and reputation 100.
- **MENU** shows stats, trust, the knowledge book, your insight log, personal portfolio, quests and relationships.

## The world

| Place | What is there |
| --- | --- |
| Ashbury | Your room above the chip shop, Carver Tower (the second flat), Lucky 7 (bet, or learn odds from the regular: +1 Risk), the off licence (pot noodle, the paper), the boarded-up Anchor, a canal, a burnt-out car. Bus stop on the main road. |
| Millbrook | Market town. Library (AFA study, borrow a book on any topic), bakery, the terrace you can move to, the green, a church. Bus stop at the bottom, platform at the top. |
| Brayford Junction | Interchange. News & Coffee (the Financial Ledger), the Junction Caff (full English, +2 energy), freight sheds. Platform 1 west, platform 4 east. |
| Lombard City | 72×56 tiles. Lombard Central and the south bank (The Splinter, Boro Market, Riverside flats, the hedge funds), a wide river with two bridges, the Square Mile on the north bank (BlackBoulder, Bank of Albion, the Royal Exchange, the Stock Exchange, the Pickle, Loyd's, the banks, Café Liquidity, the AFA Institute, the Counting House pub). Three tube stations ($3). Buses, cabs, a boat. |

## The firms

Only **BlackBoulder** is hiring in this version. The other twelve stand in the city as landmarks with a security desk; their data (salary, book, interview questions, staff, house rules) is kept in `FIRMS` so they can be switched on later by setting `hiring:true`.

| Firm | Type | Start book → ceiling | Back to the post room at |
| --- | --- | --- | --- |
| BlackBoulder | asset manager | $20M → $1B | −6% in a quarter |

Pay by rank at BlackBoulder: $95 (Office Junior), $420, $780, $1,500, $3,200 per day.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole game: CSS, maps, sprites, firms, dialogue, market sim, save/load. ~152 KB. |
| `manifest.json` | PWA manifest: name, standalone display, portrait lock, icons. |
| `sw.js` | Service worker. Own files network-first with cache fallback (works offline after first visit); fonts cache-first. Bump `CACHE` when you ship a new version. |
| `icon-180.png` | iOS home-screen icon. |
| `icon-192.png`, `icon-512.png`, `icon-512-maskable.png` | Android / Chrome icons. |

## Deploy

Put the folder contents in the root of a GitHub Pages repo (Settings → Pages → deploy from branch), or drop the folder on netlify.com/drop. Paths in `manifest.json` and `sw.js` are relative, so a project page (`user.github.io/repo/`) works as well as a root domain.

On iPhone: open in Safari → Share → Add to Home Screen. Runs full screen without browser chrome. Safari-tab storage and home-screen-app storage are separate, so a save made in the tab does not carry over to the installed app.

Saves live in `localStorage` under the key `rota.v4` (zoom level under `rota.zoom`). Nothing leaves the device. A `rota.v3` save is upgraded in place the first time the new version loads; older saves are ignored. Every save carries a `seed`: anything the world decides (which facts appear where, which event happens, who says what) is drawn from a stream keyed on the seed and the day, so reloading never rerolls a day. Only market noise is unseeded.

## How `index.html` is organised

Everything lives in one `<script>` IIFE. Sections, in order:

1. **Constants and helpers** – `T` (16 px tile), formatters, RNG, `hash()`.
2. **Sprites** – `sprite(rows, palette)` turns ASCII art into a canvas. `charFrames()` builds a 12×16 character in 4 directions × 2 frames plus a portrait. `makeTiles()` builds the tile set: office, home, nice outdoors, slum outdoors, city street furniture. `BUS_SPR`, `CAB_SPR`, `BOAT_SPR` are the traffic. No image assets anywhere.
3. **Market** – `TICK`: eight fictional stocks. Each has a hidden `drift` (the true signal) and volatility; prices move overnight as `drift + vol·noise`.
4. **Firms, ranks, knowledge** – `RANKS` (Office Junior → Analyst → Senior Analyst → Portfolio Manager → top), `CERT_FOR_RANK`, `FIRMS`, `INTERVIEWS`, `KNOW` (the six topics), `PROMO_NEED`, `BOOKS`, `INSIGHT_TEXT` (templates per topic and direction).
5. **Maps** – `OFFICE_MAP`, `HOME_SMALL`, `HOME_BIG` are ASCII grids. `mkAshbury()`, `mkMillbrook()`, `mkBrayford()`, `mkCity()` generate the towns and their building lists; `bld()` places a building (style, label, door, flavour text). `LINE`, `LEGS`, `ARRIVE`, `STOP_TEXT` describe the commute. Legend: `#` wall, `W` window, `G` glass, `D`/`d`/`P` desks, `K` kitchen, `C` coffee, `T` terminal, `E` lift or door, `b` shelf, `m` post trolley, `Q` printer, `J` junior's stool, `p` plant, `z` bed, `l` laptop, `F` fridge, `g` grass, `t` tree, `f` flowers, `h` hedge, `S` pavement, `c` cracked pavement, `w` weeds, `u` puddle, `x` rubbish, `v` burnt car, `y` parked car, `a` graffiti wall, `k` fence, `R`/`M` road, `Z` zebra crossing, `~` pond, `j` canal, `V` river, `q`/`3` quay, `1` bridge, `-` rails, `=` platform, `A` bus stop, `U` tube, `n` lamp, `e` bench, `i` phone box, `o` cobbles, `s` market stall, `B` building, `O` building door.
6. **State `S`** – everything that is saved: day, energy, cash, stats, `job` (firm, rank, trust, firm reputation, quarter P&L, rival P&L, days, promoDay), `know` (six topics), `insights` (the log: new → pitched → right/wrong), `reads`, `paper`, firm positions `pos`, personal holdings `ppos`, prices, home tier, study points and certificates, the active earnings case `ms`, the insider flag, the current scene and player position. `flags.daily` is cleared every night.
7. **Reading and insights** – `learn(topic, n, src)`, `maybeInsight()`, `readMenu()`.
8. **Quests** – `QUESTS` is derived from state, so quests never desync.
9. **Scenes and the commute** – `buildScene()`, `spawnNPCs()`, `spawnTraffic()`, `go()`, `goHome()`, `leaveHome()`. `stopDialog(dir)` lists destinations in one direction from the current stop; `travel(j, read, then)` pays, plays the montage through the intermediate towns (fade overlay `#fade` between legs) and arrives facing the stop (`ARRIVE` holds x, y, facing). `stepBack()` is the Wait option: back to the tile you came from, only if you are on a platform or a door. `tubeMenu()` is the Underground.
10. **Dialogue** – `say(npc, text, choices)` with typewriter text. `DIALOG.boss/mentor/rival/extra` branch on rank. `pitchMenu()`, `pitch()`, `promote()`. The earnings case (`startMission`, `clueText`) and the insider tip live here.
11. **Objects and places** – `interactTile()` for office, home, bus stop and tube; `doorTrigger()` for buildings; `homeDoor()`, `bookies()`, `reception()` and `interview()` (hiring firms only), café, pub, library, institute, the laptop (knowledge book, reading, study, brokerage, housing, season ticket, founding your own fund).
12. **Desk** – `deskMenu()` and the research minigame `openResearch()`, which shows your insight notes for the ticker.
13. **Day cycle** – `endDay()` moves prices, resolves pitched insights, marks positions, pays salary, collects rent, resolves the earnings event, checks eviction, the regulator and the demotion threshold, and triggers `quarter()` every 12 working days. `demote()` sends you back to the post room.
14. **Menu sheet (with the knowledge book), HUD, input, render loop (`drawBuilding()` by style: glass, steel, stone, gherkin, spire, tower, shed, station, shop, brick), boot.** Input: keyboard `held` set; the joystick `#joy` sets `joyDir`, which `update()` treats like a held key; the ACT button calls `act()`; canvas pointers are tracked in `ptrs` so a single finger lifted in place is a tap (`tap()`, BFS walk) and two fingers are a pinch (`setZoom()`); `view.zoom` multiplies the base scale of 12 tiles across and lives in `localStorage` as `rota.zoom`. The stick and button are shown on coarse pointers (`@media (pointer:coarse)`) or after the first touch, and hidden while a dialog or the montage is up.

`window.__dj` exposes `S`, `P`, `go`, `travel`, `learn`, `maybeInsight`, `promote`, `demote`, `endDay`, `quarter`, `DIALOG`, `interactTile`, `doorTrigger` and more for debugging and automated tests.

## Adding content

- **Switch a firm on:** set `hiring:true` on its `FIRMS` entry. Reception, interview, office, staff and the earnings case are generic. Its building is already in `mkCity()`.
- **New town on the line:** add a generator, an entry in `TOWN`, `TOWN_NAMES`, `LINE`, a leg in `LEGS`, arrival positions in `ARRIVE` and text in `STOP_TEXT`.
- **New building in a town:** `bld(g,B,{...})` in the generator, then handle its `kind` in `doorTrigger()`. A building with `door:false` and `text` is a landmark.
- **New reading source:** call `learn(topic, 1, src)` from anywhere; cap it with a `daily` flag.
- **New insight line:** add to `INSIGHT_TEXT[topic].pos/neg`; `{n}` is the company name, `{t}` the ticker.
- **New stock:** add to `TICK`.
- **Balance knobs:** energy per day (`HOMES`), fares (`LEGS`), `PROMO_NEED`, trust per duty (+2), insight odds in `maybeInsight()`, position sizes (15/35/60%), quarter length (12 days), rent, exam thresholds (4 and 10 study points), rival drift in `endDay()`.

## Roadmap ideas

Switch the other twelve firms on, with poaching once your reputation is known. Market-wide crash days. A client-dinner mission. NPCs on the street and on the platform. Save export/import.

## Credits

Fonts: Silkscreen and IBM Plex Sans via Google Fonts. Everything else is drawn in code. All companies, people and prices are fictional.
