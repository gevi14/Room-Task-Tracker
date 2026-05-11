# Room Tracker — Setup Guide

This sets up a real cross-device app for your team — installs to your iPhone home screen, syncs in real time between phone and desktop, and lets multiple staff check off rooms at the same time.

You'll do three things, in order:

1. Create a free Firebase project (5 min)
2. Paste the Firebase config into `index.html` (1 min)
3. Host the page (1 min) and add it to your iPhone home screen

Total time: about 10 minutes the first time, zero from then on.

---

## Step 1 — Create the Firebase project

1. Go to **https://console.firebase.google.com** and sign in with any Google account.
2. Click **Add project**. Name it anything (e.g. "Room Tracker"). You can disable Google Analytics — not needed.
3. When the project is created, click **Continue** to land on the project dashboard.

### Enable Anonymous sign-in

4. In the left sidebar, click **Build → Authentication**.
5. Click **Get started**.
6. Under **Sign-in method**, click **Anonymous**, toggle **Enable**, and **Save**.

### Create the database

7. In the left sidebar, click **Build → Firestore Database**.
8. Click **Create database**.
9. Pick a location near you (any will work). Click **Next**.
10. Choose **Start in production mode** and click **Create**.

### Paste in the security rules

11. In Firestore, go to the **Rules** tab.
12. Replace whatever is there with the contents of `firestore.rules` (the file in this folder), then click **Publish**.

### Get your config

13. In the left sidebar, click the gear icon → **Project settings**.
14. Scroll down to **Your apps** and click the `</>` (Web) icon.
15. Give the app a nickname (e.g. "Room Tracker Web"). **Don't** check "Set up Firebase Hosting" — we'll host elsewhere. Click **Register app**.
16. You'll see a code block with `const firebaseConfig = { ... };` — that's what you need.

---

## Step 2 — Paste the config into the app

1. Open `index.html` in any text editor (TextEdit, Notepad, VS Code — anything).
2. Find this block near the top of the script (around line 200):

   ```js
   const firebaseConfig = {
     apiKey:            "PASTE_YOUR_API_KEY_HERE",
     authDomain:        "PASTE_YOUR_PROJECT.firebaseapp.com",
     ...
   };
   ```
3. Replace each `PASTE_…` value with the matching value from the config Firebase showed you. Keep the quotes.
4. Save the file.

---

## Step 3 — Host it (free, no account needed)

The easiest option is **Netlify Drop** — drag, drop, done.

1. Go to **https://app.netlify.com/drop**
2. Drag the **entire `room-tracker-app` folder** into the page (not just `index.html` — the folder).
3. Netlify will give you a public URL like `https://wonderful-coyote-12345.netlify.app`.
4. Copy that URL — that's your app.

> If you want a custom name (e.g. `myhotel-rooms.netlify.app`), make a free Netlify account and rename the site in Site Settings. Not required.

---

## Step 4 — Install on your iPhone

1. Open the URL from Step 3 in **Safari** (must be Safari, not Chrome).
2. Tap the **Share** button (square with up-arrow).
3. Scroll down and tap **Add to Home Screen**.
4. Tap **Add**.

The app icon now lives on your home screen. Open it — no browser bars, looks and feels like a real app. Enter your name once, and you're tracking.

Repeat for any team member who needs access — just send them the same URL.

---

## Step 5 — Use it

- Tap a checkbox to mark a task done. Your name gets stamped on it automatically.
- Any other device using the same URL sees the update within a second.
- Use the **Reset all** button at the end of the day to clear everything (this affects everyone — there's a confirmation).
- Use **Mark floor** to mark every task on a floor complete in one tap. **Clear floor** does the opposite.

---

## Things to know

- **The URL is the access.** Anyone who has the URL and clicks "Start" gets in. Don't post it publicly — just share with your team. If you ever need to revoke access, regenerate the Firebase API key in project settings (then re-paste and re-deploy).
- **Free tier limits:** Firebase Firestore's free tier gives you 50K reads, 20K writes, and 1 GiB of storage per day. For a small team checking off rooms a few hundred times a day, you'll use roughly 1% of that.
- **Offline support:** the app works on a flaky connection — taps queue up and sync when you're back online. The header dot turns orange when offline.
- **Change your name:** tap your name pill in the header.

---

## If something goes wrong

- **"Setup needed" screen**: you didn't paste the Firebase config yet, or one of the `PASTE_…` placeholders is still in the file.
- **Can't sign in**: make sure you enabled **Anonymous** authentication in Step 1.
- **Checkboxes don't sync**: open `firestore.rules` in this folder and confirm you published those exact rules in Firestore → Rules.
- **Checks disappear after refresh**: same as above — the rules might be set to deny.

If you get stuck on any step, paste the error message and I'll walk you through it.
