# CLAUDE.md: Home Panel

One-page GitHub Pages app (index.html + icon.png) for John's iPad mini 2 on iOS 12.5.8, 1024x768 landscape.
No build step, no framework. The QR library (qrcode-generator, MIT) is pasted inline in the first script tag.

## iOS 12 Safari limits (the reason for the old-style code)
- JS: ES2017 at most. No `?.`, `??`, regex lookbehind. Check with acorn `ecmaVersion: 2017` before pushing.
- CSS: no flex `gap` (use margins; grid-gap is fine), no `inset`, `clamp()`, `aspect-ratio`.
- Parse Open-Meteo times by hand (`parseLocal`); Safari 12 misreads offset-less ISO strings.
- Sound only plays after a tap; the touchend listener wakes the audio context. The side switch can mute it.

## Privacy
Photos (IndexedDB), guest Wi-Fi (`wifi`), countdowns (`cd-list`), used trivia questions (`trivia-used`), scoreboard (`scoreboard`), movie list (`movie-list`), theme (`panel-theme`) and the weather cache (`wx`)
live only in the iPad's browser. Never put Wi-Fi passwords, family photos or home addresses in the repo; the repo is public.

## Testing
Serve over localhost, test at 1024x768 and 768x1024 in headless Chromium with the weather URL mocked.
Keep test scripts out of the repo. John is not a programmer: reply in plain words about what he'll see.

## Look
Font: Nunito (Google Fonts; falls back to the system font offline). Behind the tiles is a live sky (`renderSky`): gradient by
day/dawn/dusk/night, sun or real-phase moon crossing the header strip, stars, drifting CSS clouds, rain/snow on a 30fps canvas
(paused while a view is open), fog bands, storm flashes. Tiles and pills are translucent (`--tileA`); header text is white with a shadow.

## Layout
Home grid is full (7 tiles; Word of the day replaced the Guest Wi-Fi tile). Extra features live behind the header "Fun" button:
trivia, scoreboard, dice and coin, drawing pad, movie night picker, guest Wi-Fi, joke or riddle, word of the day.
Trivia buzzers fire on touchstart so the first finger wins; host buttons use the normal click handler with `q*` actions.
Idle return: 1 minute; a running timer never closes; trivia in progress, scoreboard and drawing pad get 10 minutes.

## Publishing
Bump `<meta name="panel-version">` on EVERY change. The iPad checks the live page every 15 minutes and reloads
itself (when idle on the home screen) only if that value differs; without a bump it keeps its cached copy.

## Bedside app (bedside/index.html)
Separate Home Screen app at /home-panel/bedside/: dim amber clock, silent sunrise wake-up (screen ramps black > red > orange > warm white
over 10/20/30 min, then a "Good morning" screen for 45 min or until tapped), sleep sounds generated with Web Audio
(white/pink/brown/rain/ocean/fan, sleep timer with 1-minute fade), night light, bedtime reminder, today/tomorrow weather.
No alarm sound by John's choice. Clock look (15 fonts in `FONTS`, each with the weights it has; a separate Light/Regular/Bold choice `S.weight`; size; color) is in the "Clock" button; fonts load
from Google Fonts on demand and the clock shrinks to fit the width. Weather (now + today/tomorrow) sits across the top.
Storage: `bedside` (settings incl. font/size/color), `bed-done`, `bed-mins`, `bed-glow`, `bed-wx`.
Has its own `panel-version` meta and the same self-update; it never reloads during a sunrise or while a sound plays.
