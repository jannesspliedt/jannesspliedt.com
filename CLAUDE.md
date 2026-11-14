# jannesspliedt.com

Personal website of Jannes Spliedt. The whole site is a 2D pixel-art roller coaster: a cart rides
from stop to stop, and each stop opens a white panel with content. Jannes usually writes in German;
the site content is in English, the Impressum is in German.

## Files

- `index.html` – everything: markup, CSS and one inline script (no build step, no dependencies).
- `posts.js` – the blog posts for the "Odds & Ends" stop, as plain text inside one template string.

## Deploying

Cloudflare (Workers Builds, project `jannesspliedt-com`) is connected to this repository.
Every push to `main` goes live at https://jannesspliedt.com within a minute or two.
There is nothing to build: the repository root is served as is.

## Hard rules

- **No external requests.** No web fonts, CDNs, analytics or embeds. The Datenschutzerklärung in the
  Impressum states that the page loads nothing external and sets no cookies. The only thing stored is
  the game state (coins and skins) in localStorage, which the Datenschutzerklärung describes. If that ever changes,
  the Datenschutzerklärung must be updated in the same commit.
- **Impressum stays reachable from everywhere** (nav button, `#impressum`, `#datenschutz`). It is a
  legal requirement in Germany. Name, address and e-mail in it were given by Jannes; do not change
  them unless he asks.
- **No phone number, home address in Aachen/Singapore or matriculation number** on the site.
- **Original artwork only.** Jannes asked for a Balrog, the hut from Shrek and Minecraft diamond ore;
  these were deliberately replaced by original designs (shadow creature with lantern eyes, mossy
  cottage, own gem-in-rock cells). Keep it that way; do not draw existing characters or game assets.
- **System fonts only**, white panels with black borders (he explicitly did not want cream/yellow).
- Keep the `no-js` fallback working: without JavaScript all panels are shown stacked.
- Respect `prefers-reduced-motion`: rides are skipped and the cart jumps to the stop.

## Stops (in order)

| Stop | id | Content |
|---|---|---|
| Start | `start` | Only the line "Press → to ride to the next stop and ← to go back." |
| Odds & Ends | `odds-and-ends` | Blog. List of posts, click opens one. Empty for now. |
| About me | `about` | Only Education, Work Experience, Languages – no prose, no contact block. Awards and scholarships are listed under the education they belong to (`small.hl`, with a star); no Aachen Investment Club (Jannes, 2026-11-14). Institution in bold, degree/role in the small line. Taken from his CV. |
| Shop | `shop` | Riders first, then carts, as buttons with a pixel preview; bought with coins. Built by `renderShop`. Without JavaScript only a note. |
| Impressum | `impressum` | Impressum and Datenschutzerklärung (German). |

Deep links: `#about`, `#shop`, `#impressum`, `#odds-and-ends/<post-slug>`.

## Blog posts (`posts.js`)

Posts are plain text between the backticks of `window.POSTS_TEXT`. Format:

```
=== Title of the post
date: 2026-11-12

Paragraph text. An empty line starts a new paragraph.

1. numbered list
- bullet list
## small heading
**bold**, *italic*, [link](https://…), ![picture](file.jpg)
```

Posts are sorted by date, newest first. No backticks inside the text. Images are files in the
repository root. The parser and renderer live in the "blog (Odds & Ends)" section of the script.

## Controls

- `→` / `d` / Space / Enter: ride to the next stop. `←` / `a`: ride back. Buttons ◀ ▶ and the nav for touch.
- Pressing the same direction again while riding = speed up. Only then the cart takes off at one
  hill crest per leg (`HOPS`), forwards and backwards.
- At Start, `←` rolls the cart back (about 3 s, can be sped up) into a spring that fires it to Odds & Ends.
- At the Impressum, `→` starts the finale: slow roll with gentle acceleration through a barrier with
  warning signs and the Karman-Tor (side view; cart passes in front of the first jamb, behind the
  second; rails in front of the first jamb), the rails end, the cart free-falls about 10 s into the
  hole with a scream bubble and an 8-bit scream (toggle "♪ on/off"), fade, and you land in the worm world.
- **Worm world** (after the fall): you are a worm in the earth and eat your way through it with arrow keys /
  WASD (click = dig towards that spot, ◀ ▶ = a few cells sideways). On touch screens a floating joystick
  appears where the finger lands (like slither.io) and steers in any angle (diagonals = alternating steps). Turning back makes the tail the head.
  The stops lie in the earth as food with their name above (Start = apple, Odds & Ends = mushroom, About me =
  carrot, Shop = plum, Impressum = pear); the head next to one opens its card, eating "Start" fades back to the coaster.
  The world is wide (280 cells; Jannes wanted the walls far apart, lots of room to dig).
  The surface is a meadow (`drawMeadow`), drawn big to suit the worm (meadow pixels are `PS` = 2 px):
  sunflowers, tulips, light blue flowers, daisies, grass, butterflies and bees, and `TREES` (oaks and pines,
  one giant oak at double size). Tree roots are solid cells (`ROOT`, `solid`): neither worm nor mole can eat
  them. Plants vanish when eaten from below.
  Scattered finds (counter in the hint): a few fossils, old coins, things from the Middle Ages higher up
  (knight helmet, sword, crown, key, shield, horseshoe, ring, goblet) and Roman ones below (amphora, helmet,
  mosaic, oil lamp). He likes variety and historical finds.
  Coin counter (bottom middle, `coins`, shared with the ride): bronze 1, silver 2, gold 4, medieval find 5,
  Roman find 10, fossils 0 (`worth`).
  A hunter mole (`H`, dark fur, pink snout and paws, mouth open, two cells big, digs a 2x2 hole) chases your head, slower than you
  (0.17 s per cell in earth vs. your 0.1 s). If it catches you: "CAUGHT!", red flash, fade, and you are
  sent all the way back to the coaster Start; the tunnels and finds are reset. It waits 5 s after you land. At the bottom is unbreakable bedrock
  (own speckled texture; Jannes asked for Minecraft bedrock, keep it an original design). In the worm
  world the nav buttons do not leave it: a yellow arrow appears and points to that stop's food (at the
  screen edge while it is off-screen, above the food once it is visible) until it is eaten. Exception for
  the law: the Impressum button also opens the Impressum card at once; it closes when the worm digs on.
- Speed label above the cart shows the measured speed (8 px = 1 m).
- Ride coins (`rideCoins`): along the flight path of each forward jump hang coins worth 1, 5, 10, 5, 1
  (bronze/silver/gold with the number, own design; Jannes wanted it like coin trails in Hill Climb Racing).
  The cart collects them in the air; they come back at every stop (`arrive`). Counter shows once coins > 0.

- **Propeller and cloud shop**: jumping back and forth keeps the cart in the air; after more than 3 s airborne
  (`airT`) a propeller appears, the cart flies up (`flying`) and lands on a hidden cloud (`world` = 2,
  `enterCloud`, `drawCloud`). The shop used to be up there; since 2026-11-14 it is a normal stop on the line between
  About me and Impressum (card `#shop`, `SHOP_I`; its leg from About me has no jump, the sky treats About me →
  Impressum as one leg via `SKY_S`; in the worm world its food is a plum). On the cloud a sign says to type
  "jannes ist der beste" into the text field `#cheat`: every time gives 10 coins, without limit (Jannes wanted
  exactly that). ↓/Esc, ◀ ▶ or a nav button lead back down. The typed text is not stored anywhere.
  `ITEMS`: rider skins and cart skins bought with `coins`. Riders are defined in `PEOPLE` (CLASSIC; MORGANE: green scarf, old brown leather jacket, black
  trousers, small silver spiral necklace; JOOST: not a person but one giant beer; JAY LAZER, that is Jannes himself: grey jumper, blue jeans,
  crown with many jewels; WOLF: a wolf in a jumper with a flag of the world, own design) and drawn by `makeCart` (in the cart) and `drawPerson` (shop tile). There are no worm
  A rider can have its own text in the scream bubble of the fall (`scream`): Wolf "GOOGLE EARTH", Joost "APRES SKI".
  Jay Lazer is the first rider on offer; while he is worn, changing direction 7 times within 2.5 s (`flips` in
  `step`) shoots laser beams from his eyes for 2.5 s (`lazT`, drawn with the cart). There are no worm
  skins any more (Jannes, 2026-11-14): the worm takes the look of the worn rider (`worn.rider.p`). Old saves
  with a worm skin get the coins back once (`OLD_WORM_SKINS`). Coins, bought skins and the worn skins are kept
  in localStorage (`SAVE_KEY`, `save`/`load`, Jannes chose this on 2026-11-14); the Datenschutzerklärung has
  a paragraph "Spielstand im Browser" for it. If more is ever stored, update that paragraph in the same commit.

## How the script is organised (inside `index.html`)

- **Track**: built from `flat`, `ramp`, `loop`, `station`, then resampled to 1 px arc length
  (`X`, `Y`, `TX`, `TY`, index = distance `s`). `stS` = stop positions.
- **Rendering**: low-resolution canvas scaled up by an integer (`SC`), `image-rendering: pixelated`.
  `VH` is the drawn height, `VHV` the visible height used for layout (phones draw past the bottom).
- **Sky** changes with progress: day → sunset → dusk → night. At night a satellite train crosses.
- **Background** (parallax 0.3, on the green hills): dinosaurs grazing (`drawDiplo`, `drawTrike`,
  `drawStego`, spread along the line via `DINO_W`) and the cottage behind the spring. A pterosaur
  flies past on the last leg (world-space, own speed).
- **Ground**: 4 px cells; `BANDS` are the rock layers. `wallL`/`wallR` define the hole.
- **Cave** (`drawCave`): burrow tunnels near the top, mine with dwarves (`drawMine`, 7 floors),
  crystals, gems in the deep stone, creatures (`drawCritters`, `drawLurker`, `drawBeast`).
- **Worm world**: section "after the fall: a worm in the earth". Grid of 4 px cells (`WC`, `WCOLS`, `WROWS`),
  `W` holds tunnels (`dug`), worm `body` (head first), keys, tap `goal`, open card. `FOODS` = stops, `FINDS` =
  fossils and coins, `isBed` = bedrock. `world` = 0 (coaster), 1 (worm) or 2 (cloud); `update`, `draw` and the keys
  branch on it (`updateWorld`, `drawWorld`, `wormKey`, `tapWorld`).
- **State**: `idx`/`target`, `riding`, `boost`, `springing`, `launched`, `wrapping`, `falling`, `world`.

## Working on it

- After changes, run the page in a headless browser and ride every leg in both directions plus the
  finale; check a phone-sized viewport too. Watch for console errors: one exception stops the animation.
- Jannes reviews visually and gives short, specific change requests; make the change, test, push.
