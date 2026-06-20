# Simple Budget

A private, self-hosted web app for daily expense tracking. Built with React + Firebase (Auth + Firestore + Hosting).

## What it does

- Sign in with Google (locked to one email address — only you can access it)
- Add daily expenses with a date, category, and amount
- Browse by month with prev/next navigation
- Monthly summary showing spending by category
- Real-time sync — works across devices instantly

---

## Setup instructions for Claude

> **Hey Claude** — the person sharing this repo wants to deploy their own instance of this budget app. Walk them through the steps below in order. Each step is self-contained.

---

### Step 1 — Prerequisites

Make sure the following are installed on the user's computer:

- **Node.js** (v18+): check with `node -v`. If missing, install from https://nodejs.org
- **Firebase CLI**: install with `npm install -g firebase-tools`

---

### Step 2 — Create a Firebase project

1. Go to https://console.firebase.google.com and sign in with the user's Google account.
2. Click **"Add project"** → give it a name (e.g. `my-budget`) → click through the wizard (Analytics is optional).
3. Once on the project dashboard, continue to Step 3.

---

### Step 3 — Enable Google Sign-In

1. In the left sidebar: **Build → Authentication → Get started**
2. Click **Google** under Sign-in providers
3. Toggle it **Enabled**, fill in a support email, click **Save**

---

### Step 4 — Enable Firestore

1. In the left sidebar: **Build → Firestore Database → Create database**
2. Choose **"Start in production mode"**
3. Pick the region closest to you (e.g. `europe-west3` for Europe, `us-central1` for US)
4. Click **Enable**

---

### Step 5 — Register a web app and get the config

1. On the project dashboard, click the **"</>"** (Web) icon
2. Give the app a nickname (e.g. `budget-web`) — do NOT tick "Firebase Hosting" here
3. Click **Register app**
4. You'll see a `firebaseConfig` object like this:

```js
const firebaseConfig = {
  apiKey:            "AIzaSy...",
  authDomain:        "my-budget-xxxx.firebaseapp.com",
  projectId:         "my-budget-xxxx",
  storageBucket:     "my-budget-xxxx.appspot.com",
  messagingSenderId: "123456789",
  appId:             "1:123456789:web:abcdef"
};
```

Copy all 6 values — you'll need them in the next step.

---

### Step 6 — Configure the app

Open `public/index.html` in a text editor. Find this section near the top (search for `REPLACE_WITH`):

```js
const firebaseConfig = {
  apiKey:            "REPLACE_WITH_YOUR_API_KEY",
  authDomain:        "REPLACE_WITH_YOUR_PROJECT_ID.firebaseapp.com",
  projectId:         "REPLACE_WITH_YOUR_PROJECT_ID",
  ...
};
```

Replace every `REPLACE_WITH_...` placeholder with the real values from Step 5.

Also find this line just below the config block:

```js
const ALLOWED_EMAIL = 'paul.czen@gmail.com';
```

**Change it to:**

```js
const ALLOWED_EMAIL = 'dimitriychernyshev@gmail.com';
```

This is the only Google account that will be allowed to sign in.

Save the file.

---

### Step 7 — Deploy to Firebase Hosting

Run these commands in the terminal from inside the project folder:

```bash
# Log in to Firebase (opens a browser window)
firebase login

# Connect the folder to your Firebase project
firebase use --add
# → Select your project from the list, type "default" as the alias

# Deploy the app and security rules
firebase deploy
```

When it finishes, you'll see a URL like:
```
Hosting URL: https://my-budget-xxxx.web.app
```

Open that URL, sign in with your Google account, and the app is live. ✅

---

### Step 8 — (Optional) Authorize your domain

If you get an "unauthorized domain" error on sign-in:
1. Go to Firebase Console → Authentication → Settings → Authorized domains
2. Add your `.web.app` domain if it's not already there

---

## Re-deploying after changes

```bash
firebase deploy --only hosting
```

---

## Data structure

Expenses are stored in Firestore under:

```
users/
  {your_uid}/
    months/
      2026-04/     ← one document per month
        days: {
          "2026-04-17": [
            { id, description, category, amount }
          ]
        }
```

---

## Security model

- **Firestore rules** (`firestore.rules`): only the signed-in user can read or write their own path. Anyone else hitting the API directly gets a permission error.
- **App-level check**: the app also verifies the signed-in email matches `ALLOWED_EMAIL`. Anyone signing in with a different Google account sees an "Access Denied" screen — their data never touches the database.

---

## Categories

| Emoji | ID | Label |
|---|---|---|
| 🍕 | food | Food & Dining |
| 🛒 | groceries | Groceries |
| 🚌 | transport | Transport |
| 🎬 | entertainment | Entertainment |
| ✈️ | travel | Travel |
| 🏠 | home | Home |
| 💊 | health | Health |
| 🛍️ | shopping | Shopping |
| 💻 | tech | Tech |
| 💰 | other | Other |
