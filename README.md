# Workout Tracker — Comeback Block

A single-file workout tracker for a 5-day Push / Pull / Legs / Upper / Lower split.
Train Mon–Wed, rest Thu, train Fri–Sat, rest Sun.

Built for a limited gym: dumbbells, barbell, a few cables, kettlebells, minimal
machines. Exercises can be swapped or randomized, and anything your gym can't
support gets filtered out.

## Running it

It's one self-contained HTML file. No build step, no dependencies, no server.

Open `index.html` in a browser and it works.

## Hosting on GitHub Pages

1. Push this repo to GitHub.
2. Repo → **Settings** → **Pages**.
3. Under *Build and deployment*, set **Source** to `Deploy from a branch`.
4. Branch `main`, folder `/ (root)`. Save.
5. It publishes to `https://<username>.github.io/<repo>/` within a minute or two.

Because the entry file is named `index.html` at the repo root, Pages serves it
with no extra configuration.

## Where your data lives

Logged weights are stored in the browser's `localStorage`, under the key the app
sets on first run.

That means:

- Data stays on the device and in the browser you entered it in.
- Clearing site data or browsing history for the domain erases the log.
- Different browsers, or the same browser on a different device, each keep a
  separate log.
- Private/incognito windows discard it when the window closes.

There is no backend and nothing is transmitted anywhere.

## Cross-device sync

The app checks for `window.claude.use("db")` at startup and, when present, syncs
the log across devices. That API only exists inside Claude's artifact runtime.

Outside it — on GitHub Pages, from a file, or any other host — the check fails
cleanly, the app falls back to `localStorage`, and the status badge reads
"Saved on this device" rather than claiming a sync that isn't happening. The
guard is a `typeof` check, so nothing throws.

If you want real sync on your own hosting you'd need to add a backend. The
integration point is the `docRef` / `onSnapshot` block at the bottom of the
script.

## External requests

The only outbound request is to Google Fonts (`fonts.googleapis.com` and
`fonts.gstatic.com`) for Anton and Barlow. Everything else — CSS, JavaScript,
layout, exercise data — is inlined in the file.

If you want it fully offline, remove the two `<link>` tags in `<head>` and the
font stacks will fall through to system defaults.

## Editing

Everything is in `index.html`:

- Exercise library and the split definition are in the data block near the top
  of the `<script>`.
- Equipment filtering is handled by `available()`.
- Persistence is `loadLocal()` and `save()`.
- The sync badge is `setSync()`.
