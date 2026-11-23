# CLAUDE.md: Home Panel

One-page GitHub Pages app (index.html + icon.png) for John's iPad mini 2 on iOS 12.5.8, 1024x768 landscape.
No build step, no framework. The QR library (qrcode-generator, MIT) is pasted inline in the first script tag.

## iOS 12 Safari limits (the reason for the old-style code)
- JS: ES2017 at most. No `?.`, `??`, regex lookbehind. Check with acorn `ecmaVersion: 2017` before pushing.
- CSS: no flex `gap` (use margins; grid-gap is fine), no `inset`, `clamp()`, `aspect-ratio`.
- Parse Open-Meteo times by hand (`parseLocal`); Safari 12 misreads offset-less ISO strings.
- Sound only plays after a tap; the touchend listener wakes the audio context. The side switch can mute it.

## Photo storage
Photos live in IndexedDB, which iOS 12 can drop or fail to reopen in Home Screen apps (WebKit bugs 197050, 200078).
Every photo also gets a 720px backup in localStorage (`photo-bk-<id>`); `loadPhotos` retries the store 3 times, restores
from backups if the store comes back empty, and shows backups if it won't open. John kept the same icon and still lost photos.

## Privacy
Photos (IndexedDB), guest Wi-Fi (`wifi`), countdowns (`cd-list`, incl. family birthdays), used trivia questions (`trivia-used`), scoreboard (`scoreboard`), movie list (`movie-list`), theme (`panel-theme`) and the weather cache (`wx`)
live only in the iPad's browser. Never put Wi-Fi passwords, family photos or home addresses in the repo; the repo is public.

## Testing
Serve over localhost, test at 1024x768 and 768x1024 in headless Chromium with the weather URL mocked.
Keep test scripts out of the repo. John is not a programmer: reply in plain words about what he'll see.

## Look
Font: Nunito (Google Fonts; falls back to the system font offline). Behind the tiles is a live sky (`renderSky`): gradient by
day/dawn/dusk/night, sun or real-phase moon crossing the header strip, stars, drifting CSS clouds, rain/snow on a 30fps canvas
(paused while a view is open), fog bands, storm flashes. Tiles and pills are translucent (`--tileA`); header text is white with a shadow.
Both apps draw a black rounded-corner frame (`#frame`, box-shadow ring) so they look like an app screen; Bedside adds a faint outline in the clock color (`#frame:after`, dimmer when idle).
Header pills: theme, "Home" (`toHome`: close any view, go to screen 1), 007, moon icon only (bedside), Fun, Don't press. The moon pill opens bedside/ inside the panel in a full-screen iframe (`openBed`/`closeBed`, `#bedFrame`, body class `bedOn` hides the rest) so iOS shows no address bar; blocked while a timer runs, asks twice during trivia. Bedside's "Panel" button posts "closeBedside" to the parent when framed, else goes to ../. The panel never self-updates while `bedOpen`.

## Layout
Two screens: swipe left/right on the home area (`#pager`, `setPage`, saved in `panel-page`; dots at the bottom).
Screen 2 (`#p2`): big photo slideshow fills the left two columns top to bottom (whole photo over a blurred copy of itself, made
by shrinking it to 16px on a canvas, so no black bars and no blur filter; or "Fill the box" = cover; change interval 5 s to 5 min;
both set on the Photos view, saved as `slide-sec` and `slide-fit`; no arrows; tap opens full view) with a right column of compact weather, countdown, verse and word. No timer, moon or joke.
Screen 1 photo tile (`tPhoto`) also cycles all photos (`showTile`, same `slide-sec`/`slide-fit`); only the visible screen's slideshow runs.
Screen 1 home grid is full (7 tiles; Word of the day replaced the Guest Wi-Fi tile). Extra features live behind the header "Fun" button:
trivia, scoreboard, dice and coin, drawing pad, movie night picker, guest Wi-Fi, joke or riddle, word of the day.
Countdown tiles (both screens) get a theme from the countdown's name or date (`themeFor`: Christmas, New Year, Valentine's, Easter,
St. Patrick's, July 4/flag holidays, Halloween, Thanksgiving, birthday, anniversary/parents, sports, fishing, vacation, graduation,
else party): SVG decorations on the right side (`decoHtml`, `SHAPES`, `THEMES`) plus a tint; the countdown list shows a theme icon.
Holidays (`HOLIDAYS`, incl. moving ones via `ruleDate`: nth/last weekday, Easter) are added once to the iPad's list (`cd-seed-hol1`).
Birthdays use the form's "It's a birthday" box: stored with `born` year, shown as "Turns N". Never hard-code family names or
birth dates in this public repo; John enters them on the iPad. Themes have 10 decorations each, some floating, plus a ground strip.
When 2+ countdowns fall within 30 days the box splits into stacked themed slices (`splitHtml`, `decoMini`): up to 3 on screen 1,
2 on screen 2. Otherwise the single big themed box.
Drawing pad "Save to photos" adds the drawing to the panel photos (IndexedDB + backup).
Trivia buzzers fire on touchstart so the first finger wins; host buttons use the normal click handler with `q*` actions.
Idle return: 1 minute; a running timer never closes; trivia in progress, scoreboard and drawing pad get 10 minutes.

## Publishing
Bump `<meta name="panel-version">` on EVERY change. The iPad checks the live page every 15 minutes and reloads
itself (when idle on the home screen) only if that value differs; without a bump it keeps its cached copy.
"Get latest version" (bottom of Fun) reloads at a fresh ?v= address. NEVER tell John to delete and re-add the Home Screen icon:
each icon has its own storage, so photos and settings are lost.
PHOTO PICKER LESSON: adding update-on-reopen (visibilitychange/pageshow listeners firing checkUpdate) coincided with the iOS 12
photo picker showing "No Photos or Videos" in the Home Screen app. Rolled back and re-added features one at a time (sky, font,
frame, manual update button, Bedside button): picker works. Do NOT add visibilitychange/pageshow handlers to the wall panel.
After any change near the photo flow, have John test Add photos on the iPad.

## Spy HQ (screen 3, `#p3`)
Third swipe screen, also reached by the green "007" header pill (`toSpy`). `setPage(n)` sets each page's translate inline (3 dots); `spyOn` starts/stops it.
Radar with tappable drifting blips (`BLIPS`, `placeBlips`, `identify`, sonar sound), mission of the day (`MISSIONS`, `spy-mission`; Accept = 5 s self-destruct with shake and static),
intel ticker (weather, nearest countdown). Gadgets: Gadget car game (`v-car`, canvas, joystick, boost/oil/smoke/eject, guards with vision cones, best time `spy-best`)
and Night vision scope (`v-scope`, drawn backyard or the panel photos, night/thermal/x-ray filters, zoom and pan, tap to lock a target, Snap saves a filtered copy to photos).
No camera by John's choice. Fonts: Share Tech Mono, Oswald. Spy views keep their "spyview" class (open/close strip only " on").
Round 2 (all 10-minute idle except agent): Vault cracker (`v-vault`, drag the dial, clicks and the stethoscope meter grow near the number, release on it to lock; 3 numbers; `spy-vault` best time),
Code breaker (`v-code`, 4 pegs, 6 colors, 10 tries; `spy-code`), Laser grid (`v-laser`, drag from START to EXIT, sliding-gap gates and spinners, 3 lives; `spy-laser` best level),
Lie detector (`v-lie`, hold thumb 5 s, random verdict; host secret: tap the title = next is LIE, tap READY = next is TRUTH; ink and paper turn light red on a LIE), Agent ID scan (`v-agent`, the only button under the radar; thumbprint pad, hold 3 s, random code name; ACCESS GRANTED leads to the Classified files, auto after 5 s).
`freeGames()` (in openView/closeView) shrinks game canvases and drops the satellite town when leaving them (iPad mini 2 memory). The Cancel/Undo pop-up is iOS Shake to Undo, not the app.
`press()` binds touch+mouse for these; `window.__spy` exists only on localhost for tests.
Round 3: Classified files `v-cls` (reached only through the Agent ID scan) (8 tiles, `cl` action); game backs use `clsback` (return to the files, not HQ).
Bomb defuse (`v-bomb`, clues each rule out one wrong wire, levels add wires and cut time, `spy-bomb`), Morse code (`v-morse`, decode game with •/— keys + translator, chart),
Spy decoder (`v-decode`, shift cipher key 1-25 incl. digits), Satellite map (`v-sat`, procedurally drawn town on a 1600px offscreen canvas, zoom/pan/pins, fake coords, infrared/night),
Disguise kit (`v-disguise`, SVG face parts + badge, saved in `spy-disguise`), Hacker terminal (`v-hack`, mash on-screen keys, bar slips back when idle),
Memory test (`v-mem`, Simon 3x3, `spy-mem`), Escape room (`v-escape`, number lock from facts, pattern, riddle; +10 s per wrong answer, `spy-escape`).

## Holiday games and football (in Fun)
Fun grid adds Football scoreboard, Thanksgiving games (`v-tg`) and Christmas games (`v-xm`); themed views use class `hview tg|xm`, `.hbar/.hback` (back to the menu via data-open), overlays `.hover` via `hOver/hOff`, loops via `hLoop(view,step)`, `fireworks(ms,colors)` on the full-screen `#fwCv`, `cheer()`, `horn()`. Emoji drawn on canvas (`EMO` font stack).
Thanksgiving: Feast Catch (`tcatch`, levels, `hg-tcatch`), Turkey Trivia (`ttriv`), Whack-a-Turkey (`twhack`, 5 levels 1P / best of 3 rounds 2P, same pop pattern on both grids, touchstart multi-touch), Pie Tower (`tstack`, `hg-tstack`), Harvest Match (`tmatch`).
Christmas: Present Drop (`xdrop`), Christmas Trivia (`xtriv`), Reindeer Race (`xrace`, 2P best of 3 or 5 levels vs Comet), Sleigh Ride (`xfly`, flappy, `hg-xfly`), Ornament Match (`xmatch`).
Trivia engine `HTRIV[tg|xm][level]` (8 questions per level, 5 or 6 asked, 20 s each, 1P needs 3 right to move up). Match engine `MSETS`, `MSIZE` 4 boards; 2P keeps the turn on a pair.
Football (`v-football`, state `fb-board`): team names (prompt), colors, timeouts, possession, quarter, game clock (keeps running when the view closes; never auto-closed), down & distance, tap/drag the field to place the ball, TD/FG/XP/2PT/safety/−1 with celebrations and fireworks, undo, New game needs two taps.
`press()` ignores touches that start on a `.hover` overlay so its buttons still click. `window.__hol` exists only on localhost for tests.

## Bedside app (bedside/index.html)
Separate Home Screen app at /home-panel/bedside/: dim amber clock, silent sunrise wake-up (screen ramps black > red > orange > warm white
over 10/20/30 min, then a "Good morning" screen for 45 min or until tapped), sleep sounds generated with Web Audio
(white/pink/brown/rain/ocean/fan, sleep timer with 1-minute fade), night light, bedtime reminder, today/tomorrow weather.
No alarm sound by John's choice. Clock look (15 fonts in `FONTS`, each with the weights it has; a separate Light/Regular/Bold choice `S.weight`; size; color) is in the "Clock" button; fonts load
from Google Fonts on demand and the clock shrinks to fit the width. The colon is drawn as two dots (`placeColons`) centered on the
digit height measured from the font, because fonts set their colon low; digits use lining figures. Weather (now + today/tomorrow) sits across the top.
Storage: `bedside` (settings incl. font/size/color), `bed-done`, `bed-mins`, `bed-glow`, `bed-wx`.
Has its own `panel-version` meta and the same self-update; it never reloads during a sunrise or while a sound plays.

## History
Nov 21: wall panel rolled back to 85987d5, then rebuilt step by step to 2026-11-21.14 (sky, Nunito, frame, Get latest version,
Bedside button). Left out for good: update-on-reopen, pauseSky, Simple look (all in git history at 097b8f7 if ever needed).
