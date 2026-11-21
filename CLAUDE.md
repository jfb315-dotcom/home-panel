# CLAUDE.md: Home Panel

One-page GitHub Pages app (index.html + icon.png) for John's iPad mini 2 on iOS 12.5.8, 1024x768 landscape.
No build step, no framework. The QR library (qrcode-generator, MIT) is pasted inline in the first script tag.

## iOS 12 Safari limits (the reason for the old-style code)
- JS: ES2017 at most. No `?.`, `??`, regex lookbehind. Check with acorn `ecmaVersion: 2017` before pushing.
- CSS: no flex `gap` (use margins; grid-gap is fine), no `inset`, `clamp()`, `aspect-ratio`.
- Parse Open-Meteo times by hand (`parseLocal`); Safari 12 misreads offset-less ISO strings.
- Sound only plays after a tap; the touchend listener wakes the audio context. The side switch can mute it.

## Privacy
Photos (IndexedDB), guest Wi-Fi (`wifi`), countdowns (`cd-list`), used trivia questions (`trivia-used`), theme (`panel-theme`) and the weather cache (`wx`)
live only in the iPad's browser. Never put Wi-Fi passwords, family photos or home addresses in the repo; the repo is public.

## Testing
Serve over localhost, test at 1024x768 and 768x1024 in headless Chromium with the weather URL mocked.
Keep test scripts out of the repo. John is not a programmer: reply in plain words about what he'll see.

## Layout
Home grid is full (7 tiles). Extra features live behind the header "Fun" button (trivia, joke or riddle, word of the day).
Trivia buzzers fire on touchstart so the first finger wins; host buttons use the normal click handler with `q*` actions.
Idle return: 1 minute; a running timer never closes; a trivia game in progress gets 10 minutes.

## Publishing
Bump `<meta name="panel-version">` on EVERY change. The iPad checks the live page every 15 minutes and reloads
itself (when idle on the home screen) only if that value differs; without a bump it keeps its cached copy.
