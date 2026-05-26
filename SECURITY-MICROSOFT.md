# Adding "Sign in with Microsoft" (Hybrid M365 setup)

This adds a **"Sign in with Microsoft"** option to the login screen alongside the existing PIN+name flow. Supervisors and you (admin) can sign in with your work M365 accounts; housekeepers keep using PIN+name on shared phones.

You'll do four things, in order:

1. Register an app in **Azure AD / Microsoft Entra** so Firebase can talk to it.
2. Enable **Microsoft** as a sign-in provider in **Firebase Auth**.
3. Add a **staff doc keyed by email** for each M365 user you want to allow.
4. Upload the new `index.html`.

Allow about 15 minutes. Most of it is Azure AD setup; it's a one-time thing.

---

## Step 1 — Register the app in Azure AD (Microsoft Entra)

1. Go to **https://portal.azure.com** and sign in with your **M365 admin** account.
2. In the search bar at the top, type **Microsoft Entra ID** (formerly Azure Active Directory) and click it.
3. In the left sidebar, click **App registrations** → **+ New registration** at the top.
4. Fill in:
   - **Name:** `Room Tracker` (or anything you'll recognize)
   - **Supported account types:** pick **"Accounts in this organizational directory only — single tenant"**. (This restricts sign-in to your company's M365 users only — exactly what you want.)
   - **Redirect URI:** dropdown set to **Web**, and the value:
     ```
     https://room-task-tracker.firebaseapp.com/__/auth/handler
     ```
     (Replace `room-task-tracker` with your Firebase project ID if it's different — you can find it in Firebase Console → Project settings → General.)
5. Click **Register**.

You'll land on the app's Overview page. **Copy these two values** somewhere — you'll need them in Step 2:

- **Application (client) ID** — looks like `12345678-abcd-1234-abcd-1234567890ab`
- **Directory (tenant) ID** — same shape, different value

### Create a client secret

Still on the Azure app page:

1. Left sidebar → **Certificates & secrets**.
2. Tab: **Client secrets** → click **+ New client secret**.
3. Description: `Firebase Auth`
4. Expires: **180 days** (or whatever your policy allows — you'll need to rotate when it expires).
5. Click **Add**.
6. **Copy the "Value" field IMMEDIATELY** — you can only see it once. It's a string like `aBc~ABc.123XyZ-456`. If you lose it you'll have to make a new one.

---

## Step 2 — Enable Microsoft as a sign-in provider in Firebase

1. Open **https://console.firebase.google.com** → your project → **Build → Authentication** → **Sign-in method** tab.
2. Scroll the list of providers, find **Microsoft**, click it.
3. Toggle **Enable** to on.
4. Paste:
   - **Application (client) ID:** the one you copied from Azure
   - **Application secret:** the **Value** you copied immediately after creating the client secret
5. Optional — **Tenant:** if you set "single tenant" in Azure, paste your Directory (tenant) ID here too. This restricts sign-in to only your company's M365 users (recommended).
6. Click **Save**.

There may be a "Authorized domains" section asking you to confirm `gevi14.github.io` and the Firebase domain are listed. They should already be there from earlier setup. If not, add `gevi14.github.io`.

---

## Step 3 — Add staff docs keyed by email for M365 users

Each M365 user who should be allowed in needs a Firestore staff doc keyed by their **lowercase email**. This is just like adding a PIN staff member, but using their email instead of their name.

Sign in to the app (with your existing PIN), open the console with F12, paste:

```js
(async () => {
  const { getApp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-app.js");
  const { getFirestore, doc, setDoc, serverTimestamp } = await import("https://www.gstatic.com/firebasejs/10.13.0/firebase-firestore.js");
  const db = getFirestore(getApp());
  const email = (prompt("Email of the M365 user to add (e.g. geverson@hotel.com):") || "").trim().toLowerCase();
  if (!email || !email.includes("@")) return console.warn("Cancelled or invalid email.");
  const name = (prompt("Display name for that person (e.g. Geverson Dos Santos):") || "").trim();
  if (!name) return console.warn("Cancelled.");
  const role = (prompt("Role: 'admin' or 'staff' (default staff):") || "staff").toLowerCase();
  await setDoc(doc(db, "staff", email), {
    name, email, role: role === "admin" ? "admin" : "staff",
    active: true, authMethod: "microsoft",
    createdAt: serverTimestamp()
  });
  console.log(`✓ Added ${name} (${email}) as ${role}. They can now sign in with Microsoft.`);
})();
```

Run it once per M365 user you want to allow. The doc ID is the lowercase email so the app's rules can find them via the auth token's email claim.

> **Tip:** Add your own M365 account first with `role: "admin"`. That gives you M365 sign-in access AND keeps your PIN admin access — both work.

---

## Step 4 — Upload the new `index.html`

The updated HTML has:
- A "Sign in with Microsoft" button at the top of the login screen
- The existing PIN+name fields below it
- A divider in between

Same routine as before:

1. Go to **https://github.com/gevi14/Room-Task-Tracker/upload/main**.
2. Drag `index.html` from `C:\Users\gdossantos\Room_Tracker\room-tracker-app\` onto the page.
3. Click **Commit changes**.
4. Wait ~30 seconds.
5. Hard-refresh the live app (Ctrl+Shift+R).

---

## Test it

1. Open the live app. You should see the new login screen with:
   - **Sign in with Microsoft** button (top)
   - **OR USE PROPERTY PIN** divider
   - Name + PIN fields and Start button (bottom)
2. Click **Sign in with Microsoft**. A popup window appears.
3. Sign in with your M365 work account. Approve any permission prompt (you're approving the "Room Tracker" app to read your name and email).
4. The popup closes and you should land in the app, signed in as your real M365 name. Header shows your name; admin buttons (Activity, Edit tasks, Reset all) appear if your staff doc has `role: "admin"`.

### Common errors and fixes

- **"Microsoft sign-in isn't enabled yet in Firebase"** → Step 2 wasn't completed. Go enable Microsoft in Firebase Auth → Sign-in method.
- **"isn't on the staff list"** → Run the Step 3 snippet to add that email.
- **Azure popup says "AADSTS50011: The redirect URI specified in the request does not match"** → The Azure redirect URI in Step 1 must match Firebase's exactly. Re-check it: `https://YOUR-PROJECT-ID.firebaseapp.com/__/auth/handler`.
- **"This app is not configured for use with your account type"** → You probably set Azure to "single tenant" but tried to sign in with a personal Microsoft account. Either use your work M365 account, or change Azure to "multi-tenant" (less secure).

---

## What this gives you vs. PIN-only

| | PIN + name | Sign in with Microsoft |
|---|---|---|
| Identity guarantee | Soft (anyone can type a name from the list) | Strong (verified by Microsoft) |
| PIN management | Rotate periodically | None needed |
| Staff list management | Manual snippet | Manual snippet (by email) |
| Audit log accuracy | Name as typed | Real verified identity |
| Works on shared phones | Yes | Awkward (each user has to sign in/out) |
| Cost | $0 | Requires M365 license for each signer |
| Best for | Housekeepers on shared devices | You, supervisors, anyone with M365 |

That's why we keep both. Housekeepers continue with the PIN on shared phones; you and supervisors use M365 for true accountability.

---

## Removing or rotating

- **Deactivate an M365 user:** open the app, F12 console, run the deactivate snippet from `SECURITY-SETUP.md` and use their email instead of their name.
- **Rotate the Azure client secret:** Azure portal → your app registration → Certificates & secrets → create new secret → paste new "Value" into Firebase Auth → save. Delete the old secret in Azure.
- **Disable Microsoft sign-in entirely:** Firebase Console → Auth → Sign-in method → Microsoft → Disable. Existing M365 users instantly lose access; PIN users unaffected.
