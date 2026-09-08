# Email — Room Tracker app update (Hotel view, column %, cycle dates)

Copy the body below into your email client. Fill in `[Your name]` before sending.

> **Note before sending:** nothing in this update changes how anyone signs in — the PIN and your name on the staff list still work exactly the same. Just a hard-refresh after the new version is live and you're done.

---

**Subject:** Room Tracker — new things on the tracker (please hard-refresh)

**To:** [your team distribution list]

---

Hi team,

A few new things just went live on the tracker. Nothing changes how you log in — same name, same PIN — but you'll see new buttons and numbers when you open it. Quick rundown so you're not caught off guard.

## What's new

**1. PBR is now the home page.**
When you open the tracker, the first thing you see is a **PBR** overview — a big centered banner with the whole property's percentage, a progress bar, the total tasks complete, and an East / West split underneath. Below the banner you'll see each task (Bed Pads, Shower Bottles, etc.) on its own card, also showing an East / West breakdown so you can spot which tower is dragging a task down. The floor checklist is hidden in PBR view since it's just an overview — tap **East** or **West** in the bar at the top to check rooms as usual.

**2. Home button up top.**
A small house icon now sits in the header next to the dark/light toggle. Tap it from anywhere to jump back to the PBR home view. Handy when you've drilled into a floor or task and want to get back to the property overview.

**3. A percentage on every task column.**
On the floor view, right under each task name in the table header, there's now a small colored percentage telling you what fraction of *this floor's* rooms have that task done. Below the percentage there's a small `19/20` style count so you can see exact numbers. When a column hits 100%, the count disappears (since 100% says it all). Colors follow the same rule as the summary cards: red is low, orange is mid, yellow is getting there, and green is done.

**4. Smoother colors near 100%.**
Percentages between 90% and 100% used to jump from yellow straight to green at exactly 100. Now they slide gradually — 91% is barely shifted from yellow, 95% is noticeably trending green, 99% is almost green, 100% is full green. Tiny touch, easier on the eyes when you're scanning.

**5. Cycle dates on certain tasks.**
You might see a small line on some task cards that reads something like *"Started May 10 · Target May 20 · 4d left"* with a colored chip on the right. This is a tracking cycle — I (or whoever's admin) started a clock on that task and set a target date for when it should be done property-wide. The colored chip on the right tells you how the deadline is going: green if you're on pace, yellow as the target gets close, red if it's overdue. You don't have to do anything special — just keep checking rooms off as usual. When the whole property hits 100% on that task, the cycle closes automatically.

## What to do right now

1. Open the live app: https://gevi14.github.io/Room-Task-Tracker/
2. **Hard-refresh** so you get the new version:
   - On iPhone: close the app from your app switcher and re-open it from the home screen.
   - On desktop browser: Ctrl + Shift + R (Windows) or Cmd + Shift + R (Mac).
3. You should land on the new **PBR** home screen with the big property percentage at the top. If you don't, refresh again — your phone is still on the cached old version.

That's it. Sign in like always (name + PIN). The PIN is the same one you've been using.

If you spot anything broken, or something looks weird on your screen, reply to this email and I'll fix it.

Thanks team.

[Your name]

---

## After-sending checklist for you

- Once you confirm everyone sees the new Hotel button, you know the rollout worked.
- The cycle-timer feature is admin-only on the *start* side. To kick off a cycle for a task, open the live app, tap the small **▶ Start cycle** pill on any task card, pick a target date, save. Everyone then sees the date info on that card. The full setup notes are in `SECURITY-SETUP.md` already.
- To see active cycles and history, tap **Activity** in the app header.
- No Firestore rule changes were needed for this release. Just upload `index.html` to GitHub if you haven't already.
