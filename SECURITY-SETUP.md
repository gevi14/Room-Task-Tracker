# Security setup — Tier 1 (Name allowlist + Property PIN)

This guide turns on the Tier 1 security model. Do these steps **in order** — if you change the Firestore rules before seeding the data, you'll lock yourself out and have to undo the rules first.

You'll do four things:

1. Seed the initial data (PIN + staff list) using a one-time browser-console snippet.
2. Update Firestore rules.
3. Upload the new `index.html`.
4. Enable Firestore TTL on the `activity` collection so audit logs auto-clean after 30 days.

---

## Step 1 — Seed initial data (PIN + staff)

While the current (permissive) rules are still active, open the **live app** in Chrome:

https://gevi14.github.io/Room-Task-Tracker/

Sign in with any name (the existing permissive rules still allow this). Then press **F12** → **Console** tab. Paste the block below.

When it asks for input, you'll be prompted for:
- The **PIN** you want everyone to use (4–6 digits is fine — staff will type this on every login).
- A comma-separated list of **staff names** (e.g. `Geverson, Rodrigo, Maria, Carlos`).
- The **admin name** (you) — must be one of the names above.

```js
(async () => {
  const { getApp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-app.js");
  const { getFirestore, doc, setDoc, serverTimestamp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-firestore.js");
  const { getAuth } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-auth.js");

  const app = getApp();
  const db = getFirestore(app);
  const auth = getAuth(app);
  if (!auth.currentUser) { console.error("Not signed in — enter your name and click Start in the app first."); return; }

  const pin = prompt("Property PIN (4–6 digits) everyone will type at login:")?.trim();
  if (!pin) return console.warn("Cancelled.");
  const staffCsv = prompt("Staff names, comma separated (e.g. Geverson, Rodrigo, Maria):")?.trim();
  if (!staffCsv) return console.warn("Cancelled.");
  const adminName = prompt("Which of these names should be the admin?")?.trim();
  if (!adminName) return console.warn("Cancelled.");

  // Generate a random salt and hash the PIN
  const saltBytes = crypto.getRandomValues(new Uint8Array(12));
  const salt = [...saltBytes].map(b => b.toString(16).padStart(2,"0")).join("");
  async function sha256(str) {
    const buf = await crypto.subtle.digest("SHA-256", new TextEncoder().encode(str));
    return [...new Uint8Array(buf)].map(b => b.toString(16).padStart(2,"0")).join("");
  }
  const pinHash = await sha256(`${salt}::${pin}`);

  await setDoc(doc(db, "config", "property"), {
    pinHash, salt, updatedAt: serverTimestamp(), updatedBy: "bootstrap"
  });

  const names = staffCsv.split(",").map(s => s.trim()).filter(Boolean);
  const adminLower = adminName.toLowerCase();
  let adminFound = false;
  for (const name of names) {
    const lower = name.toLowerCase();
    const isAdmin = lower === adminLower;
    if (isAdmin) adminFound = true;
    await setDoc(doc(db, "staff", lower), {
      name,                             // canonical display name (preserves caps)
      role: isAdmin ? "admin" : "staff",
      active: true,
      email: "",                        // optional, future Tier 2
      createdAt: serverTimestamp()
    });
  }
  if (!adminFound) {
    console.warn(`⚠ Admin name "${adminName}" was not in the staff list — no admin was set. Re-run with a name from the staff list.`);
  } else {
    console.log(`✓ Configured: PIN set, ${names.length} staff added, admin = ${adminName}`);
  }
})();
```

Press Enter, fill in the three prompts, and you should see `✓ Configured: ...` in the console.

> Save the PIN somewhere safe. The PIN is not stored anywhere readable in plain text — only its salted hash. If you forget it, you'll have to re-run this snippet to set a new one.

---

## Step 2 — Update Firestore rules

The new strict rules are in `firestore.rules` next to `index.html`. To apply them:

1. Firebase Console → your project → **Build → Firestore Database → Rules tab**.
2. Replace everything in the editor with the contents of `firestore.rules`.
3. Click **Publish**.

> If you publish before doing Step 1, the bootstrap snippet will fail (rules will block writes). Do them in order.

---

## Step 3 — Upload the new `index.html`

1. Go to **https://github.com/gevi14/Room-Task-Tracker/upload/main**.
2. Drag the updated `index.html` from your `room-tracker-app` folder onto the page.
3. Click **Commit changes**.
4. Wait ~30 seconds for GitHub Pages to rebuild.
5. Hard-refresh the app (Ctrl+Shift+R). You should now see the new login screen with two fields: **Your name** and **Property PIN**.

---

## Step 4 — Enable Firestore TTL on the `activity` collection

This makes audit-log entries auto-delete after 30 days so storage doesn't grow forever.

1. Firebase Console → **Build → Firestore Database** → **TTL** tab (you may need to click the dotted-menu/"more" button to find it).
2. Click **Create policy**.
3. **Collection:** `activity`.
4. **Field:** `expireAt`.
5. Save.

Firestore TTL deletes documents within 24 hours of their `expireAt` time. Each new audit entry sets that field to "now + 30 days" automatically.

---

## How to do day-to-day admin tasks

### Add a new staff member

Open the app, sign in, F12 → Console → paste:

```js
(async () => {
  const { getApp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-app.js");
  const { getFirestore, doc, setDoc, serverTimestamp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-firestore.js");
  const db = getFirestore(getApp());
  const name = prompt("New staff name (case as you want it shown):")?.trim();
  if (!name) return;
  const role = (prompt("Role: 'admin' or 'staff' (default staff):") || "staff").toLowerCase();
  await setDoc(doc(db, "staff", name.toLowerCase()), {
    name, role: role === "admin" ? "admin" : "staff", active: true, email: "", createdAt: serverTimestamp()
  });
  console.log(`✓ Added ${name} as ${role}`);
})();
```

### Deactivate someone who left (no delete — preserves audit history)

```js
(async () => {
  const { getApp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-app.js");
  const { getFirestore, doc, updateDoc } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-firestore.js");
  const db = getFirestore(getApp());
  const name = prompt("Name to deactivate:")?.trim();
  if (!name) return;
  await updateDoc(doc(db, "staff", name.toLowerCase()), { active: false });
  console.log(`✓ Deactivated ${name}`);
})();
```

### Change the property PIN

```js
(async () => {
  const { getApp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-app.js");
  const { getFirestore, doc, setDoc, serverTimestamp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-firestore.js");
  const db = getFirestore(getApp());
  const pin = prompt("New PIN:")?.trim();
  if (!pin) return;
  const saltBytes = crypto.getRandomValues(new Uint8Array(12));
  const salt = [...saltBytes].map(b => b.toString(16).padStart(2,"0")).join("");
  async function sha256(s) { const b = await crypto.subtle.digest("SHA-256", new TextEncoder().encode(s)); return [...new Uint8Array(b)].map(x => x.toString(16).padStart(2,"0")).join(""); }
  const pinHash = await sha256(`${salt}::${pin}`);
  await setDoc(doc(db, "config", "property"), { pinHash, salt, updatedAt: serverTimestamp() }, { merge: true });
  console.log("✓ PIN updated. Everyone uses the new PIN on their next login.");
})();
```

### View the audit log

In the app header (when signed in as admin), tap the **Activity** button. The 200 most recent entries appear, newest first. Older entries auto-delete after 30 days.

---

## What changed under the hood

- **Login screen** now requires a Property PIN in addition to your name.
- **Firestore rules** check that your auth-token display name matches a doc in `/staff/` and that doc has `active: true`. Any other client (including someone with the Firebase config and the URL) is rejected at the database level — not just the UI.
- **Admin actions** (Reset all, Activity log) are gated to users whose `staff` doc has `role: "admin"`.
- **Audit log** records every check, uncheck, column-mark, column-clear, floor-mark, floor-clear, and reset. Entries include who, what, and when. TTL deletes after 30 days.

## Migrating to Tier 2 later

The staff docs already include an `email` field placeholder. When you're ready for Tier 2:

1. Fill in everyone's `email` field.
2. Update Firestore rules to check `request.auth.token.email` (lower-cased) against `staff` docs.
3. Switch Firebase Auth → **Email Link** sign-in.
4. Replace the PIN+name login screen with the email-link screen.

No data loss, the audit log, completions, and staff roles all carry over.
