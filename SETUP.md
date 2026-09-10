# Boardly Live — Setup Guide

Boardly Live is a static GitHub Pages front end with Firebase Realtime Database as the real-time backend.

## What the live version does

- Multiple students can open the same board at the same time.
- Posts appear for everyone immediately.
- Moving, resizing, editing, checking tasks, colors, links, and board settings synchronize live.
- Each student enters a display name.
- The top-right area shows who is online.
- Every board has a shareable room code and URL.
- Presence is removed automatically when a student disconnects.
- Images are compressed in the browser and stored with the board, so Firebase Cloud Storage is not required.
- The front end remains one `index.html` file and can be hosted on GitHub Pages.

## Firebase setup

### 1. Create a Firebase project
Go to the Firebase Console and create a project.

### 2. Add a Web App
Inside the project:
- Open **Project settings**
- Under **Your apps**, choose the Web `</>` icon
- Register a web app
- Firebase will show a `firebaseConfig` object

### 3. Create Realtime Database
- Open **Build → Realtime Database**
- Create a database
- Choose a region near your users
- You can start in locked mode

### 4. Enable Anonymous Authentication
- Open **Build → Authentication**
- Open **Sign-in method**
- Enable **Anonymous**

### 5. Add your GitHub Pages domain
In Firebase Authentication settings, add your GitHub Pages domain to **Authorized domains** if it is not already accepted.

Example:
`yourusername.github.io`

### 6. Install the database rules
Open:
**Realtime Database → Rules**

Replace the existing rules with the contents of `firebase-rules.json`:

```json
{
  "rules": {
    "boards": {
      "$boardId": {
        ".read": "auth != null",
        ".write": "auth != null"
      }
    }
  }
}
```

Publish the rules.

### 7. Paste Firebase config into index.html

Open `index.html` and find:

```js
const FIREBASE_CONFIG = {
  apiKey: "PASTE_YOUR_API_KEY",
  authDomain: "PASTE_YOUR_PROJECT.firebaseapp.com",
  databaseURL: "https://PASTE_YOUR_DATABASE_URL",
  projectId: "PASTE_YOUR_PROJECT_ID",
  storageBucket: "PASTE_YOUR_STORAGE_BUCKET",
  messagingSenderId: "PASTE_YOUR_MESSAGING_SENDER_ID",
  appId: "PASTE_YOUR_APP_ID"
};
```

Replace the placeholder object with the exact configuration Firebase gives you.

The Firebase web configuration is designed to be included in browser code. Security comes from Authentication and Firebase Security Rules, not from hiding the Web API key.

### 8. Upload to GitHub
Upload `index.html` to your repository root.

Then open:
**Repository → Settings → Pages**

Choose:
- **Deploy from a branch**
- Branch: `main`
- Folder: `/ (root)`

## Classroom workflow

### Teacher
1. Open the site.
2. Enter your name.
3. Click **Create new board**.
4. Click the Share button.
5. Send the generated URL or board code to students.

### Students
1. Open the shared URL.
2. Enter their name.
3. Click **Join board**.
4. Everyone can now post and work on the canvas simultaneously.

## Important permissions note

This starter classroom version is intentionally collaborative: authenticated participants who know a board code can edit the board.

If you later need:
- teacher-only deletion,
- students editing only their own posts,
- locked teacher posts,
- moderation/approval before publishing,
- Google school-account login,
- class rosters,
- board passwords,
- permanent student accounts,

those should be added with stronger Firebase security rules and teacher/student roles.

## Images

This build compresses uploaded images before placing them in Realtime Database. That avoids needing Firebase Cloud Storage.

For large-scale image/video/file use, switch attachments to Cloud Storage or another storage service.
