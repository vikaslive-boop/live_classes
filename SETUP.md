# DBMCI One: setup (Firebase + GitHub)

Do the parts **in this order**. Your current site keeps working until Part 4,
and the appreciation photos are never touched.

Firebase Console: https://console.firebase.google.com → project **live-360-6f7e6**

---

## Part 1: Turn on Google sign-in (Firebase)

1. Left menu → **Build → Authentication**. If you see **Get started**, click it.
2. Tab **Sign-in method** → **Add new provider** → **Google**.
3. Switch **Enable** on → **Project support email**: pick your email → **Save**.
4. Tab **Settings** → **Authorized domains** → **Add domain** → type your site's
   domain **without** `https://` or any path:
   - GitHub Pages: `yourusername.github.io`
   - Custom domain: e.g. `dashboard.dbmci.one`
   → **Add**.

## Part 2: Add the people who may enter (Firebase)

1. Left menu → **Build → Firestore Database** → tab **Data**.
2. Click **+ Start collection** → Collection ID: `members` → **Next**.
3. First document:
   - **Document ID:** your Google email in lowercase, e.g. `vikas.singh@dbmci.one`
   - **Field:** `role` · Type **string** · Value `superadmin`
   - **Save**.
4. Click **+ Add document** inside `members` for each other super admin (same way).

Only super admins need to be added here. Everyone else you add later inside the app
(**Admin → Members**).

Roles: `viewer` (see everything, download photos, export PDF) · `admin` (also upload,
delete photos, edit faculty) · `superadmin` (also manage members, see activity log).

> The email must be the exact Google account the person signs in with. A work
> address like `name@dbmci.one` only works if that domain uses Google Workspace;
> otherwise use their Gmail address.

## Part 3: Put the new files on GitHub

1. Open your repository on github.com.
2. Delete the old `index.html`, `login.html`, `pdf.html`, `firebase.js` and
   `your-main-script.js` (open each file → ⋯ → **Delete file** → **Commit**).
   **Keep `dbmci-one-logo.png`.**
3. **Add file → Upload files** → drag in everything from this folder:
   `index.html`, `login.html`, `pdf.html`, `firebase.js`, `firestore.rules`,
   `SETUP.md` and the whole **`assets`** folder (drag the folder itself).
   → **Commit changes**.
4. Wait 1–2 minutes for GitHub Pages to publish.

## Part 4: Test before locking

1. Open `https://yourusername.github.io/<repo>/login.html`
   (press **Ctrl + Shift + R** once to skip the old cached version).
2. **Continue with Google** → choose your account → the dashboard opens.
3. Check Attendance, Issues, Ratings and the Appreciation wall all show data.
4. **Appreciation wall → Download photos → tick All faculty → Download ZIP.**
   Save this ZIP safely. It's your full photo backup.

## Part 5: Lock the database (Firebase)

1. **Firestore Database → Rules** tab.
2. Delete everything in the editor, paste the full contents of `firestore.rules`
   → **Publish**.
3. **Data** tab → collection `facultyHub` → document `adminCredentials` → ⋮ →
   **Delete document**. (It holds the old password list. Delete only this one
   document; leave `facultyList` and `adminOptions`.)
4. Do **not** delete anything in `screenshots` or `facultyScreenshots`. Both hold
   appreciation photos: `facultyScreenshots` has the older ones, which the dashboard
   now shows read-only.

## Part 6: Final check

1. Reload the dashboard: everything still loads.
2. Open the site in a private window with a Google account that is **not** on the
   list → you should see "does not have access".
3. **Admin → Members** → add your team's emails with their roles.

---

## If something goes wrong

| What you see | Fix |
|---|---|
| "This website address is not yet allowed" / `unauthorized-domain` | Part 1, step 4: add the exact domain shown in the browser bar. |
| "…does not have access to DBMCI One" for yourself | The `members` document ID must be your Google email in lowercase, field `role` spelled exactly, value `superadmin`. |
| Dashboard loads but no photos, or "Missing or insufficient permissions" | Rules were published but your `members` entry is wrong. Fix the entry, then reload. |
| Google popup closes instantly / blocked | Allow popups for your site. On phones the app switches to a full-page sign-in automatically. |
| Old design still showing | Hard refresh (Ctrl + Shift + R) or wait a few minutes for GitHub Pages. |

**Undo:** if you need to roll back quickly, Firestore → Rules → **History** lets you
restore the previous rules with one click.

## Day-to-day

- **New cycle, course, subject, day or “Part” column:** just add it in the Google
  Sheet. The dashboard picks it up automatically.
- **New team member:** Admin → Members → their Google email + role.
- **Someone leaves:** Admin → Members → remove. They're locked out immediately.
- People are signed out after 30 minutes without activity.

## Not covered: the Google Sheet links

The sheets are read through "Publish to web" CSV links, which anyone with the link
can open. To close that later, sync the sheet into Firestore with a scheduled
Apps Script, or serve it through a Cloud Function that checks the login.
