# Room Task Organizer — Project State

Last updated: May 11, 2026

## What this is

A tracker for hotel housekeeping. Four concurrent jobs per room — **Bed Pads, Shower Bottles, Lotion, Beacon** — checked off as they're completed. Multiple staff use it from their phones; everyone sees updates in real time. Installs to the iPhone home screen as a PWA.

## Room configuration (locked in)

**East Tower** — floors 2–8, 3-digit room numbers
- Floor 2: rooms 203–223
- Floor 3: rooms 303–323
- Floor 4: rooms 403–423
- Floor 5: rooms 503–523
- Floor 6: rooms 603–623
- Floor 7: rooms 703–723, **skip 713**
- Floor 8: rooms 803–822 (stops at 22), **skip 813**

**West Tower** — floors 2–9, 4-digit room numbers (each floor starts with the floor number)
- Floor 2: rooms 2001–2026, **skip 2006**
- Floor 3: rooms 3001–3026, **skip 3006**
- Floor 4: rooms 4001–4026, **skip 4006**
- Floor 5: rooms 5001–5026, **skip 5006**
- Floor 6: rooms 6001–6026, **skip 6006**
- Floor 7: rooms 7001–7026, **skip 7006**
- Floor 8: rooms 8001–8026, **skip 8006 and 8013**
- Floor 9: rooms 9001–9026, **skip 9006 and 9013**

There is no "Main Tower" — only East and West.

## What's been built

Three versions exist in the `outputs/` folder:

1. **`room-tracker.html`** — original local-only version. Saves checkboxes to the browser only, no cross-device sync. Was registered as a Cowork artifact named `room-task-organizer`.
2. **`room-tracker-app/`** — the real app. Single-file HTML + Firebase. Multi-user, real-time, installable on iPhone. Three files inside:
   - `index.html` — the app
   - `SETUP.md` — step-by-step deploy guide
   - `firestore.rules` — security rules for Firebase
3. **`tracker-app.zip`** — zipped copy of `room-tracker-app/` for easy transfer.

The Firebase app uses:
- Firebase Anonymous Auth (everyone enters a display name, no passwords)
- Firestore collection `completions`, doc id `${tower}-${room}-${job}`, fields `{ tower, room, job, doneBy, doneAt }`
- Real-time `onSnapshot` listener
- localStorage for UI prefs and saved display name

## Where setup stands

Geverson started the Firebase setup, then hit deployment trouble:
- Tried **Netlify Drop** — kept failing to sign up. Google sign-in errored, never made it past the Netlify signup screen.
- Decided to switch to **Vercel** instead. Path that was being walked:
  1. Sign up at github.com
  2. Create a `room-tracker` repo, upload the 3 files via GitHub's web UI
  3. Sign up at vercel.com using GitHub login
  4. Import the GitHub repo, click Deploy
  5. Add the resulting Vercel URL to Firebase → Authentication → Settings → Authorized domains
  6. Open the URL in Safari on iPhone, Add to Home Screen

Status of Firebase config inside `index.html`: **placeholders not yet replaced**. The `PASTE_…` values in the `firebaseConfig` block need to be filled in with values from his Firebase project before the app will work.

## Open questions / future direction

He raised a possible rebuild: **React + Next.js + Turso (hosted SQLite) + Drizzle ORM, deployed on Vercel**. Reasoning: real SQL queries, cleaner long-term codebase, full-featured stack. Tradeoff vs. current Firebase app:
- Pro: SQL, easier reports/history, idiomatic Vercel stack
- Con: needs GitHub web edits to redeploy, ~1–2 sec sync lag (vs. sub-second Firestore), build step

Not started yet. The plan was to get the current Firebase app working first.

## To pick this up in a new Claude session

Open a new chat and paste:

> I'm continuing a project. Please read `PROJECT-STATE.md` in my outputs folder for context, then we'll keep going.

Or just paste the contents of this file directly.
