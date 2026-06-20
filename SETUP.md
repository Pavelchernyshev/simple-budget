# Budget App — Firebase Setup & Deploy Guide

This guide walks you from zero to a live, private budget app in ~15 minutes.

---

## Step 1 — Create a Firebase Project

1. Go to **https://console.firebase.google.com** and sign in with your Google account.
2. Click **"Add project"**.
3. Give it a name (e.g. `simple-budget`) and click through the setup wizard.
   - Analytics is optional — you can disable it.
4. Once created, you'll land on the project dashboard.

---

## Step 2 — Enable Firestore Database

1. In the left sidebar, click **"Build" → "Firestore Database"**.
2. Click **"Create database"**.
3. Choose **"Start in production mode"** (the security rules file handles access).
4. Pick a region close to you (e.g. `europe-west3` for Frankfurt) and click **"Enable"**.

---

## Step 3 — Enable Google Sign-In

1. In the left sidebar, click **"Build" → "Authentication"**.
2. Click **"Get started"**.
3. Under **"Sign-in providers"**, click **"Google"**.
4. Toggle it **Enabled**, fill in your project name and support email, then click **"Save"**.

---

## Step 4 — Add your Web App & get the config

1. On the project dashboard, click the **"</>"** (Web) icon to add a web app.
2. Give the app a nickname (e.g. `budget-web`) — **do not** enable Firebase Hosting here yet.
3. Click **"Register app"**.
4. You'll see a code block like this — **copy the values**:

```js
const firebaseConfig = {
  apiKey:            "AIzaSy...",
  authDomain:        "simple-budget-xxxx.firebaseapp.com",
  projectId:         "simple-budget-xxxx",
  storageBucket:     "simple-budget-xxxx.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123456789:web:abcdef"
};
```

5. Open **`public/index.html`** in a text editor.
6. Find the section near the top that says `★ PASTE YOUR FIREBASE CONFIG HERE ★`
   and replace the placeholder values with your real values.

---

## Step 5 — Authorize your domain

1. In **Authentication → Settings → Authorized domains**, make sure you see:
   - `localhost` (for testing)
   - `YOUR_PROJECT_ID.web.app` (added automatically when you enable Hosting)

---

## Step 6 — Install Firebase CLI & deploy

Open a terminal (on your computer, not in this app) and run:

```bash
# Install Firebase CLI globally (requires Node.js — install from nodejs.org if needed)
npm install -g firebase-tools

# Log in with your Google account
firebase login

# Navigate to this folder
cd "path/to/budget-online"

# Connect this folder to your Firebase project
firebase use --add
# → Select your project from the list, give it an alias like "default"

# Deploy everything (hosting + firestore rules)
firebase deploy
```

When deploy finishes you'll see a **Hosting URL** like:
```
https://simple-budget-xxxx.web.app
```

Open it, sign in with your Google account, and you're live. 🎉

---

## Step 7 — Enable Firestore in the Firebase Console

After deploying once, go back to:
- **Firestore → Rules** and confirm the rules match what's in `firestore.rules`.

---

## Security model

- **Firestore rules**: only the authenticated user can read/write their own path (`/users/{uid}/...`). Anyone else trying to access your data via the API gets a permission error.
- **App-level check**: the app also checks that the signed-in Google email matches `paul.czen@gmail.com`. Anyone else signing in with a different Google account sees an "Access Denied" screen and their data never touches Firestore.

---

## Data structure

Each month is stored as one Firestore document:

```
users/
  {your_uid}/
    months/
      2026-04/
        days: {
          "2026-04-17": [
            { id, description, category, amount }
          ]
        }
      2026-03/
        ...
```

---

## Re-deploying after changes

```bash
firebase deploy --only hosting
```

---

## Testing locally before deploy

```bash
firebase emulators:start --only hosting,firestore
```

Then open `http://localhost:5000`.
