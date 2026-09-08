# Email — Room Tracker security & feature update

Copy the body below into your email client. Fill in `[Your name]` before sending.

> **A note before sending:** the PIN is included in the email below because you asked for it that way. Email isn't a perfectly secure channel — anyone who can forward, screenshot, or access the recipient's inbox could see the PIN. For a tighter handoff, you could send the PIN separately via SMS or in person and just say "the PIN is what I sent you separately" in the email. Either way works, just heads-up.

---

**Subject:** Room Tracker — security update + new features (please read before next login)

**To:** [your team distribution list]

---

Hi team,

I just rolled out a security update plus some new features on the Room Tracker app. **The way you sign in has changed**, so please read this before you next open the tracker.

## How to log in now

The login screen now has **two fields**:

1. **Your name** — must match the name I have registered for you on the staff list. Use the exact spelling I gave you (Geverson, Rodrigo, Maria, etc.). If your name isn't on the list, you won't get in.
2. **A property PIN** — same for everyone on the team:

> **Property PIN:** `27200`

Please **keep this PIN confidential** — don't post it on a wall, don't text it to anyone outside the team, and don't share it with vendors or guests. If you suspect it's been leaked, let me know and I'll change it.

## What's new in the app

- **Real names required.** No more typing whatever you want. Your check-marks are now tagged to your actual staff name automatically.
- **Activity log (admin-only).** I can now see a full history of who did what and when — every check, uncheck, mark-floor, clear-floor, and reset. The log keeps the last 30 days. This is for accountability, not surveillance. Day-to-day, you'll never need to think about it.
- **Reset is admin-only.** The big "Reset all" button is gone for non-admins, so no more accidental wipes of the whole day's work.
- **Closet Hooks** is the fifth task on every room (new column on the floor table).
- **East Tower Floor 1** — rooms 114, 117, 118 — is now in the system.
- **Color-coded percentages** at the top — red, orange, yellow, green — tell you at a glance how each task is tracking.
- **"Select all" pill** under each task column — one tap marks the whole column done for the current floor.
- **Click any task %** at the top to open a detail view showing every room with that task across both towers. Filter by All / Completed / Incomplete.
- **Dark / light theme toggle** — sun/moon icon in the header.

## What to do right now

1. Open https://gevi14.github.io/Room-Task-Tracker/ on your phone in **Safari** (not Chrome).
2. If you previously added it to your home screen, just tap the icon as usual. The new login screen will appear.
3. Enter your name and the PIN.
4. Tap Start — you're in.

If you see an error:

- **"Wrong PIN"** → double-check you typed `27200` exactly.
- **"That name isn't on the staff list"** → reply to this email and I'll add you in under a minute.
- **"Your account is inactive"** → same as above, reply and I'll reactivate.

If you've never installed the app on your phone before, open the URL in Safari, tap the **Share** button, then **"Add to Home Screen"**.

Thanks for rolling with the change.

[Your name]

---

## After-sending checklist for you

- Once you confirm everyone can log in, this email and the PIN are your single source of truth. Keep this email in a folder you can find again.
- When someone joins or leaves, run the "add staff" or "deactivate" snippets from `SECURITY-SETUP.md` in your outputs folder.
- Rotate the PIN every few months (or immediately if someone leaves who you don't fully trust). Just re-run the "change PIN" snippet — everyone uses the new one on their next login.
- When the team is ready (Tier 2), we can swap to email-link login instead of PIN. The data model is already prepared for it.
