# To BIM, or Not To BIM — a classroom board game

A live, multiplayer web game for teaching **BIM vs CAD** decision-making on an AEC
(Architecture / Engineering / Construction) project. Five students per group play a
**Client**, a **Design Team** (2), and a **Contractor** (2), bidding fees and rolling
dice for quality, time, and profit across two rounds. Built for a class of up to nine
groups, played on their phones, with a projector dashboard for the debrief.

Originally an Excel board game; this is the web version.

## What's in this repo

| File | What it is |
|------|-----------|
| `index.html` | The game. Teachers host a session; students join on their phones. |
| `dashboard.html` | Live class-results dashboard for projecting, with Excel/CSV export. |
| `README.md` | This file. |

Both are self-contained single HTML files. The only backend is a free **Firebase
Realtime Database** that syncs devices live.

---

## Sessions (how records stay separated)

Every class run is a **session** with a unique, date-stamped code (e.g.
`20260917-7QF2`). All of that class's data lives under `sessions/<code>/` in the
database, so different teachers, classes, and dates never collide. The teacher
creates a session, shares its link, and the dashboard reads that one session.

---

## Run it for your class (self-host — ~10 minutes, free, private)

Each teacher runs their **own** copy so your data is yours and you're not limited by
anyone else's usage. Firebase's free (Spark) plan is far more than a class needs.

### 1. Create the Firebase backend
1. Go to <https://console.firebase.google.com> → **Add project** (any name; Analytics optional).
2. **Build → Realtime Database → Create Database** → pick a location → start in test mode.
3. Open the **Rules** tab, paste this, and **Publish**:
   ```json
   {
     "rules": {
       "sessions": {
         "$sid": { ".read": true, ".write": true }
       }
     }
   }
   ```
   *(Access is limited to game sessions; the list of all sessions cannot be read. Fine
   for classroom use — no personal data beyond the names students type. You can delete
   the database anytime.)*
4. **Project settings (gear) → Your apps → Web (`</>`)** → register an app → copy the
   `firebaseConfig` object.

### 2. Paste your config into both files
In **`index.html`** and **`dashboard.html`**, find the `window.FIREBASE_CONFIG = { … }`
block near the top and replace the values with your own (`apiKey`, `authDomain`,
`databaseURL`, `projectId`, `appId`; `databaseURL` is the important one). These web
keys are safe to ship publicly — the security rules do the protecting.

### 3. Publish (GitHub Pages, free)
1. Create a **public** GitHub repo.
2. **Add file → Upload files** → drag in `index.html`, `dashboard.html`, `README.md` → **Commit**.
3. **Settings → Pages** → Source: **Deploy from a branch** → `main` / `/root` → **Save**.
4. Your links (after ~1 min):
   - Game: `https://<user>.github.io/<repo>/`
   - Dashboard: `https://<user>.github.io/<repo>/dashboard.html`

Any static host works (Netlify, Vercel, your LMS) — it's just two HTML files.

---

## Running a class

1. Open the game → **Host a session** → enter your university and play date → **Create session**.
2. On the group-join screen, tap **Copy join link** and share it (chat, QR, slide). Tap
   **Copy results-dashboard link** for yourself.
3. Students open the link, pick **Group 1–9**, then their **role**. Five phones per group.
4. Play: Client plans → design bidding → deploy architects + BIM/CAD, roll → quality
   check → contractor bidding → construction → results. Round 2 adds competitive sealed
   bidding between the two designers and the two contractors.
5. Open the **dashboard** (already scoped to your session), press **Fullscreen**, and
   project. It updates live as groups finish.
6. Click **⬇ Excel** (or CSV) to save the full record — one file per class, stamped with
   your session code, university, and date.

Solo / pass-the-phone mode is available from the landing screen for trying the mechanics
on one device.

---

## Game rules in brief

- **Budget** = construction fee × 1.2 (the extra 20% is the design fee). **Quality demand**
  = budget ÷ 50; the design phase must reach ⅙ of it, construction the other ⅚. **Time
  allowance** per phase equals that phase's quality demand.
- Each work round you hire one **labor tier** (Super / Senior / Junior) and one **tool**
  (BIM / CAD), pay both, and roll a die for each → quality 👍 and time ⌛. BIM rolls like
  a Super, CAD like a Senior. Engineers/tools in construction are ×5.
- **Profit:** Client = budget − fees + quality×50 − (time − demand)×15. Design/Contractor
  = fee − spend − (time − allowance)×15 − overhead (+ a $50 BIM discount per matched round
  for the contractor). Finishing under the time allowance earns a bonus; going over costs
  $15 per unit. Switching CAD→BIM mid-project adds an overhead fee.

---

## Notes & limits

- **Free tier:** ~100 simultaneous connections and 1 GB storage — plenty for one class;
  self-hosting keeps each teacher within their own quota.
- **Reset:** a session resets from the game's **Reset game** control, or delete
  `sessions/<code>` (or the whole database) from the Firebase console between semesters.
- **Privacy:** the only stored personal data is the display names students enter.

Made with Claude Code.
