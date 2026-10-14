# runfourcode live chat + project request setup

## Files
- `index.html` — main website; dashboard links point to `./SRC/Dashboard.html`.
- `SRC/Dashboard.html` — Firebase sign-in, project request form, request status and client live chat.
- `SRC/Admin.html` — admin/executive panel, live request queue, approve/reject controls, online/offline availability and live replies.
- `firestore.rules` — Firestore security rules for these collections.

## 1. Put files in this exact layout

```text
project-root/
├── index.html
├── firestore.rules
└── SRC/
    ├── Dashboard.html
    └── Admin.html
```

Upload all four files to the same hosting root/folder structure. Use exact capitalization for `SRC`, `Dashboard.html`, and `Admin.html`.

## 2. Configure the admin email BEFORE deploying

In `SRC/Admin.html`, replace:

```js
const ADMIN_EMAILS = ["YOUR_ADMIN_EMAIL@example.com"];
```

with the actual Firebase Authentication email for each trusted admin/business executive, e.g.:

```js
const ADMIN_EMAILS = ["your-real-admin@yourdomain.com", "executive@yourdomain.com"];
```

In `firestore.rules`, replace `YOUR_ADMIN_EMAIL@example.com` with the primary admin email. If multiple executives need access, add their emails to the `isAdmin()` rule using `||` checks. Keep the admin list private to trusted staff.

## 3. Firebase console setup

1. Open Firebase Console and select project `runfourcode`.
2. Build → Firestore Database → create a database if it does not exist.
3. Open Firestore → Rules, paste the contents of `firestore.rules`, replace the admin email placeholder, then Publish.
4. Authentication → Sign-in method: enable Email/Password. Create the admin account under Authentication → Users. Enable Google provider if clients should use Google login.
5. Authentication → Settings → Authorized domains: add the domain hosting this website.
6. Test using the deployed HTTPS website, not only by opening the HTML file locally.

## 4. Workflow

- A client signs in and sends a project request.
- Firestore stores the request immediately; it appears live in `SRC/Admin.html`.
- EmailJS sends the existing enquiry email using the unchanged public key, service ID and template ID. If EmailJS fails, the Firestore request still remains in the admin panel.
- Client chat works while the request is pending; client and admin messages update in real time.
- Admin can approve/reject each request and toggle team availability.

## Security note

Do not publish the site until the admin email placeholders are replaced and Firestore rules are published. The client-side admin email list only controls the UI; Firestore rules are what enforce access to data. For a larger team or higher-security production use, move admin authorization to Firebase custom claims set by a trusted server/Admin SDK. Never put service-account credentials in these HTML files.
