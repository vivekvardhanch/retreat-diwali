# Retreat Diwali Planning — Community Diwali Event Portal

A single static web page (`index.html`) for planning the community Diwali event.
Data is stored in Google Firebase (Cloud Firestore) and shared by everyone who opens the site.

## Files

| File | Publish? | Purpose |
|---|---|---|
| `index.html` | **Yes** | The portal. Contains no personal data. |
| `firestore.rules` | No (deploy to Firebase) | Database security rules. |
| `index-with-data.html` | **Never publish** | Same app with the 52 families from the spreadsheet preloaded. Contains phone numbers, emails and addresses. |
| `README.md` | Optional | This guide. |

## 1. Firebase setup (one time, about 10 minutes)

1. Go to https://console.firebase.google.com and click **Create a project**. Name it (for example `retreat-diwali`). You can turn off Google Analytics.
2. In the left menu, open **Build → Firestore Database → Create database**. Choose a location near you (for example `us-east4`). Start in **production mode**; you will replace the rules in step 5.
3. Open **Build → Authentication → Get started → Sign-in method**. Click **Anonymous**, switch it on, and save. (The app signs in anonymously so that the rules can require a signed-in session. Committee members do not log in.)
4. Open **Project settings** (gear icon) → **General** → **Your apps** → click the `</>` (Web) icon. Register an app name, leave Firebase Hosting unticked, and click **Register app**. Copy the `firebaseConfig` values.
5. Open **Firestore Database → Rules**. Delete the existing text, paste the full contents of `firestore.rules`, and click **Publish**.
6. Open **Authentication → Settings → Authorized domains** and click **Add domain**. Enter `YOUR-GITHUB-USERNAME.github.io`. (`localhost` is already listed.)

## 2. Put your config into the page

Open `index.html` in a text editor and find `const FIREBASE_CONFIG = {` near the top of the script. Replace the placeholder values with the ones you copied in step 1.4:

```js
const FIREBASE_CONFIG = {
  apiKey: 'AIza...',
  authDomain: 'retreat-diwali.firebaseapp.com',
  projectId: 'retreat-diwali',
  storageBucket: 'retreat-diwali.appspot.com',
  messagingSenderId: '123456789',
  appId: '1:123456789:web:abc123'
};
```

These values are not secret. Firebase web config is meant to be public. Access control comes from the security rules in `firestore.rules`, not from hiding this config.

## 3. Publish on GitHub Pages

1. Create a GitHub account if you don't have one. Click **New repository**, name it (for example `retreat-diwali`), and set it to **Public**. Free GitHub Pages requires a public repository (private repositories need a paid GitHub plan).
2. On the repository page, click **Add file → Upload files**. Drag in **`index.html`** and **`README.md`** only. Do **not** upload `index-with-data.html`.
3. Click **Commit changes**.
4. Go to **Settings → Pages**. Under **Build and deployment → Source**, choose **Deploy from a branch**. Set **Branch** to `main` and the folder to `/ (root)`. Click **Save**.
5. Wait 1–3 minutes. Refresh the Pages settings page; it shows your address: `https://YOUR-GITHUB-USERNAME.github.io/retreat-diwali/`.
6. Open that address. Go to **Settings** in the portal. It should say **Connected to Firebase — shared**.

## 4. Load the spreadsheet data (private)

Choose one of these:

- **Option A (recommended):** open the site, go to **Settings → Tabs & data → Upload Excel / CSV**, choose the registrations spreadsheet, and follow the review steps. Nothing is saved until you click Import.
- **Option B:** open `index-with-data.html` **on your own computer** (double-click it). Use **Settings → Export all to Excel** or **Version History → Restore** to move the data where you need it. Do not host this file.

## 5. Update the site later

1. On GitHub, open the repository and click `index.html → ⋯ → Edit`, or use **Add file → Upload files** to replace it.
2. Commit. The site updates within a minute or two. Hard-refresh the page (Ctrl+F5 or Cmd+Shift+R).

## 6. Troubleshooting

| Symptom | Fix |
|---|---|
| 404 Not Found on the Pages address | `index.html` must be at the root of the repository, and Pages must be set to `main` / `/ (root)`. |
| Settings says **Local only — not shared** | `FIREBASE_CONFIG` still has placeholder values. |
| Settings says **Offline — saved in this browser** | Check the config values, that Anonymous sign-in is enabled, and that `YOUR-USERNAME.github.io` is in Authorized domains. |
| Save fails with *permission-denied* | The rules in `firestore.rules` were not published (step 1.5). |
| "Someone saved at the same moment" message | Two people saved together. Your last change was refused, so re-enter it. Nothing was overwritten. |

## Access and privacy (read this)

- **Anyone who has the web address can read and edit the event data.** Committee members do not need accounts, so the rules allow any anonymous session to read and write.
- The rules protect against accidental overwrites, deletions and changes to the audit trail. They do not make the data private.
- Do not publish personal details (phone numbers, emails, addresses, dietary notes) to a public site unless you accept that anyone with the link can see them. If you need privacy, add committee-only sign-in (for example Google sign-in limited to committee emails) and tighten the rules before loading personal data.

## Limits

- All event data is stored as one shared record, which holds up to about 1 MB. The 52 registrations use about 22 KB. If the event grows much larger, the data should be split into separate records.
- Firebase's free Spark plan covers this event's size easily.

## Local development

Open `index.html` in a browser. Without a Firebase config it runs in local mode, with data saved in that browser only.
