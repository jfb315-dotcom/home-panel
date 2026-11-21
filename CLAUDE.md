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
Both apps draw a black rounded-corner frame (`#frame`, box-shadow ring) so they look like an app screen; Bedside adds a faint outline in the clock color (`#frame:after`, dimmer when idle).
Header "Bedside" pill goes to bedside/ (blocked while a timer runs; asks twice during trivia); Bedside's "Panel" button goes back.

## Layout
Home grid is full (7 tiles; Word of the day replaced the Guest Wi-Fi tile). Extra features live behind the header "Fun" button:
trivia, scoreboard, dice and coin, drawing pad, movie night picker, guest Wi-Fi, joke or riddle, word of the day.
Trivia buzzers fire on touchstart so the first finger wins; host buttons use the normal click handler with `q*` actions.
Idle return: 1 minute; a running timer never closes; trivia in progress, scoreboard and drawing pad get 10 minutes.

## Publishing
Bump `<meta name="panel-version">` on EVERY change. The iPad checks the live page every 15 minutes and reloads
itself (when idle on the home screen) only if that value differs; without a bump it keeps its cached copy.
It also checks on reopen (visibilitychange/pageshow). "Get latest version" (bottom of Fun; Bedside's Clock look screen) reloads at a
fresh ?v= address. NEVER tell John to delete and re-add the Home Screen icon: each icon has its own storage, so photos and settings are lost.
iOS 12 Home Screen apps have a photo-picker bug ("No Photos or Videos"). It started after the live sky was added (memory on a 1 GB iPad
was suspected), so the sky is fully torn down while the Photos view is open (`pauseSky`). That did not fix it, and a fresh icon
is also empty. "Simple look" (`panel-simple`, bottom of Fun) turns off the web font, sky, see-through tiles and frame for testing.
Fallbacks: Files > Browse, or run in Safari.

## Bedside app (bedside/index.html)
Separate Home Screen app at /home-panel/bedside/: dim amber clock, silent sunrise wake-up (screen ramps black > red > orange > warm white
over 10/20/30 min, then a "Good morning" screen for 45 min or until tapped), sleep sounds generated with Web Audio
(white/pink/brown/rain/ocean/fan, sleep timer with 1-minute fade), night light, bedtime reminder, today/tomorrow weather.
No alarm sound by John's choice. Clock look (15 fonts in `FONTS`, each with the weights it has; a separate Light/Regular/Bold choice `S.weight`; size; color) is in the "Clock" button; fonts load
from Google Fonts on demand and the clock shrinks to fit the width. The colon is drawn as two dots (`placeColons`) centered on the
digit height measured from the font, because fonts set their colon low; digits use lining figures. Weather (now + today/tomorrow) sits across the top.
Storage: `bedside` (settings incl. font/size/color), `bed-done`, `bed-mins`, `bed-glow`, `bed-wx`.
Has its own `panel-version` meta and the same self-update; it never reloads during a sunrise or while a sound plays.

## Rollback (Nov 21, 4 PM)
John asked to roll the wall panel back to commit 85987d5 (before Bedside, live sky, Nunito, frame, Bedside button) to test
whether the photo picker works again. index.html = 85987d5 with panel-version bumped to 2026-11-21.11. The later version is in
git history (097b8f7). Re-add later features one at a time, testing the photo picker after each. Bedside app files are unchanged.
