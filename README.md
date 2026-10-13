# DiceHammer
A simple dice rolling app designed to make those shooting Ork hoards hitting on 6+ go a bit faster.

## Use it
Open `index.html` in any browser. It's a single file with no build step or dependencies, so it works offline once loaded.

- **Pick your pool**: drag from the top-left of the 8×8 tray to drop up to 64 D6 in, or use − / +.
- **Roll**: tap the target face (2+ … 6+). Successes are outlined, failures dim, 6s glow and sparkle, 1s show a skull.
- **Re-roll 1s / Re-roll fails**: re-rolls only those dice and keeps the rest.
- **Remove fails**: drops failures so your hits become the next pool (e.g. hit roll → wound roll).
- **Log**: every roll this session; tap an entry to restore those dice.

Rolls use the browser's cryptographic random generator (`crypto.getRandomValues`) with rejection sampling, so every face is exactly equally likely.

## Live app
https://kristopherencarnacao-cmyk.github.io/DiceHammer/

Every push to `main` redeploys the site automatically through GitHub Actions (`.github/workflows/pages.yml`). One-time setup: **Settings → Pages → Source: GitHub Actions**.
