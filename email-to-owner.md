# Email to Owner — Room Task Tracker

Copy the text below into your email client. Fill in the bracketed parts before sending.

---

**Subject:** Housekeeping room tracker — quick prototype to take a look at

---

Hi [Owner's name],

I wanted to share something I've been working on this week — a digital tracker for our housekeeping team that lets us log room-by-room task completion in real time, from both desktop and phone.

You can take a look here: **https://gevi14.github.io/Room-Task-Tracker/**

**How it works.** Every room in both towers is in the system — East Tower floors 1–8 and West Tower floors 2–9, with the exact room ranges and skipped numbers that match our property. Each room has five tasks: Bed Pads, Shower Bottles, Lotion, Beacon, and Closet Hooks. As housekeepers complete each task, they tap a checkbox on their phone. Updates sync instantly across every device, so when one team member marks a room on the floor, supervisors see it on the dashboard within a second. Every completion is stamped automatically with the staff member's name and the exact time. The page also installs to a phone home screen like any normal mobile app, so the team can open it with one tap.

**Where it stands today.** What you're looking at is a working HTML prototype. It's fully functional and ready to use right now, but I built it quickly to prove the concept and to get something real in front of you. The foundation is solid — multi-device sync, accountability, mobile-friendly — but the surface is still simple.

**Where this is headed.** Now that the basics work, my next step is to rebuild it on a proper framework. That will let us expand without starting over each time — adding new task categories, generating daily and weekly reports, tracking per-person productivity, running shift-by-shift breakdowns, or extending the same idea to other areas of the operation. Today's version covers five tasks; the next version is designed to scale to as many as we want.

I'm sharing this now so you can see the direction before I invest in the bigger build. Take a look when you have a minute — happy to walk you through it in person whenever works for you.

Best,
[Your name]

---

## Notes on the email

- The link works on any device. The owner can open it on their phone and tap through the same way you do.
- If they ask "is this safe?" the short answer is: data is hosted on Google's Firebase free tier, sync is encrypted, only people with the URL can access it, and we can lock it tighter with email logins anytime.
- If they ask "what does this cost?": $0 today. Firebase free tier covers our scale (thousands of checks per day) with room to spare.
- If they ask "what would the bigger build need?": you can tell them you'll come back with a short proposal once they're interested.
