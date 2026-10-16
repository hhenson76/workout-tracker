# Workout Tracker

A five-day training log for getting back under the bar after time off. Push / Pull / Legs / Upper / Lower, with a weight and rep field for every set, last week's numbers under each row, and an exercise library that filters itself down to the equipment you actually have — including a one-tap switch between dumbbell and kettlebell versions of the whole program.

No build step, no dependencies, no account. Static files you can drop on GitHub Pages and open from your phone.

## What it does

- **Five sessions a week**, structured so each muscle group gets trained roughly twice: Mon Push, Tue Pull, Wed Legs, Thu rest, Fri Upper, Sat Lower, Sun rest.
- **Log every set** — weight and reps (or seconds, for holds and carries), plus a done toggle. Saves as you type.
- **Week-by-week history.** Each week is stored separately and the previous week's numbers appear under every set row, which is what double progression needs.
- **Dumbbell / kettlebell toggle.** A switch above each session sets which tool the picker reaches for first. On Kettlebell, all 37 slots fill with kettlebell movements; slots with no kettlebell version keep the best thing your gym has.
- **Equipment filtering.** Switch off the machines your gym doesn't have and every exercise needing them disappears, replaced by one that trains the same muscle with what you do have. 228 exercises, 101 of them kettlebell; the plan still fills completely at bodyweight-only.
- **Exercise swaps.** Each card is a *slot* — "primary chest press", "hamstring curl" — not a fixed lift. Re-roll one card or shuffle the whole day. Optional auto-vary rolls a fresh selection each new week.
- **No repeats within a session.** Pools are shared between slots, so the picker tracks what a day has already used and won't serve you two floor presses or two sets of swings in one workout.
- **Coaching built in.** Reps-in-reserve targets shift by week (3–4 RIR in weeks 1–2, down to 1–2 by week 5), with rest periods and the progression scheme in the Guide sheet.
- **Installable and offline.** A service worker caches the app shell, so it opens in a dead-signal basement gym and can be added to your home screen.

## Files

Everything sits at the repo root — no subfolders, so the GitHub web uploader can't scramble the structure.

```
index.html               markup and page head
app.css                  all styling, light and dark themes
app.js                   plan data, exercise library, state, rendering
icon.svg                 source icon
icon-192.png             home-screen icon
icon-512.png             splash / maskable icon
manifest.webmanifest     makes it installable
sw.js                    offline cache
.nojekyll                tells GitHub Pages to serve the files as-is
LICENSE                  MIT
```

## Putting it on GitHub Pages

1. Create a repository. Public is simplest; Pages on a private repo needs a paid plan.
2. Upload every file above so they all sit at the repo root. On github.com: **Add file → Upload files**, then drag them in. There are no subfolders to preserve.
3. **Settings → Pages**. Under *Build and deployment*, set **Source** to `Deploy from a branch`, branch `main`, folder `/ (root)`. Save.
4. Wait a minute or two, then open `https://<your-username>.github.io/<repo-name>/`.
5. On your phone, open that URL and add it to your home screen — **Share → Add to Home Screen** on iOS, **⋮ → Add to Home screen** on Android.

HTTPS matters here: the service worker and the install prompt only work over HTTPS (or `localhost`). GitHub Pages gives you HTTPS automatically.

## Running it locally

Opening `index.html` straight off disk works for everything except the service worker, which needs a real origin:

```
python3 -m http.server 8000
```

Then open `http://localhost:8000/`.

## Where your data lives

In your browser's `localStorage`, on that device, under the key `workout-tracker-v1`. It never leaves the device and there's no server involved. Logs written before the app was renamed live under `comeback-block-v2`; the app reads that key once if the new one is empty.

Your phone and your laptop keep separate logs, and clearing site data wipes it. **Guide → Export backup** writes a JSON file; **Import backup** reads one back. Export before switching phones.

## Changing the plan

Everything lives in `app.js`:

- `EQUIP` / `DEFAULT_EQ` — the equipment list and which switches start on.
- `SIDE_DELT`, `REAR_DELT`, `HAM_CURL`, `CALF`, `TRI_MAIN`, `TRI_LONG`, `BICEP`, `BRACH`, `ABS` — pools shared across days.
- `PLAN` — the five days, each a list of slots. A slot sets the role, set count and rep range; `opts` is the list of exercises that can fill it.

An exercise is `o(id, name, equipment, extra)`:

```js
o("kb-goblet", "Goblet Squat", ["kettlebell"])
o("bw-plank",  "Plank", [], { bw: true, time: true })
o("db-lunge",  "Dumbbell Walking Lunge", ["dumbbell"], { perLeg: true })
```

`equipment` is an array of `EQUIP` ids — all of them must be switched on for the exercise to appear. An empty array means bodyweight, always available. In `extra`: `bw` replaces the weight field with a "Bodyweight" marker, `time` logs seconds instead of reps, `perLeg` labels the field "per side", and `lo` / `hi` override the slot's rep range for that one exercise.

Two rules that keep the picker honest:

- Give every slot at least one option with an empty equipment array, and the plan can never strand you with an unfillable card.
- **Order matters.** Within a slot, the first option available for the current preference is the default pick, so put the movement you'd actually choose first.

After editing any file, bump `CACHE` in `sw.js` — otherwise phones that already installed it keep serving the old version from cache.

## Sources

The kettlebell library was built from these references:

- [Kettlebell exercises by muscle group — Kettlebells Workouts](https://kettlebellsworkouts.com/kettlebell-exercises-by-muscle-group/)
- [Best kettlebell chest exercises — Barbell Medicine](https://www.barbellmedicine.com/blog/best-kettlebell-chest-exercises/)
- [11 best kettlebell back exercises — Fitness Volt](https://fitnessvolt.com/best-kettlebell-back-exercises/)
- [Kettlebell lat workouts — Kettlebell Kings](https://www.kettlebellkings.com/blogs/default-blog/kettlebell-lat-workouts)
- [Kettlebell shoulder exercises — Mirafit](https://mirafit.co.uk/kettlebell-shoulder-exercises/)
- [Kettlebell leg exercises and workouts — SET FOR SET](https://www.setforset.com/blogs/news/kettlebell-leg-exercises-and-workouts)
- [12 kettlebell arm exercises — Kettlebell Kings](https://www.kettlebellkings.com/blogs/default-blog/12-kettlebell-arm-exercises-biceps-and-triceps-exercises)
- [13 best kettlebell core exercises — Fitness Volt](https://fitnessvolt.com/kettlebell-core-exercises/)

## Licence

MIT. It's your training log; do what you like with it.
