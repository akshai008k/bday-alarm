# 🔔 Birthday Bell v5.0

A feature-rich birthday reminder web app with custom ringtones, Firebase cloud sync, and a sleek dark theme. Single HTML file — no build tools, no dependencies to install.

---

## Features

- **Smart reminders** — alerts 7 days before, 1 day before, and on the birthday itself
- **Custom ringtones** — upload your own MP3/WAV/OGG files as alarm tones
- **Per-contact ringtone** — assign a different ringtone to each birthday
- **Firebase cloud sync** — sign in with Google to sync birthdays and ringtones across devices
- **Gift tracking** — log gifts given each year per contact
- **Calendar view** — see birthdays laid out on the current month
- **History tab** — browse all contacts with age info and gift counts
- **Confetti animation** — fires on birthday alarms
- **Export / Import** — back up and restore your data as JSON
- **Offline-first** — works fully with localStorage when not signed in
- **Subtle dark theme** — slate-navy palette, easy on the eyes

---

## Quick Start

1. Download `birthday-bell.html`
2. Open it in any modern browser
3. Start adding birthdays — no sign-in required

That's it. Everything runs locally in the browser by default.

---

## Firebase Setup (optional, for cloud sync)

Firebase is optional. Without it, all data is stored in `localStorage` on your device. To enable sync:

### 1. Create a Firebase project

Go to [console.firebase.google.com](https://console.firebase.google.com) → **Add project** → follow the steps.

### 2. Enable services

Inside your project:

- **Authentication** → Sign-in method → enable **Google**
- **Firestore Database** → Create database → start in **test mode** (lock it down later with rules)
- **Storage** → Get started → start in **test mode**

### 3. Get your config

Go to **Project Settings** → **Your apps** → **Add app** → Web (`</>`). Copy the config object that looks like:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "your-app.firebaseapp.com",
  projectId: "your-app",
  storageBucket: "your-app.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123:web:abc"
};
```

### 4. Paste into the file

Open `birthday-bell.html` in a text editor. Find the `firebaseConfig` block near the bottom and replace the six `YOUR_*` placeholders with your real values.

### 5. Set Firestore security rules

In the Firebase Console → Firestore → Rules, replace the default with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

### 6. Set Storage CORS (if ringtone uploads fail)

If you see CORS errors when uploading ringtones, create a file called `cors.json`:

```json
[
  {
    "origin": ["*"],
    "method": ["GET", "PUT", "POST", "DELETE"],
    "maxAgeSeconds": 3600
  }
]
```

Then run in the [Google Cloud Shell](https://shell.cloud.google.com):

```bash
gsutil cors set cors.json gs://your-app.appspot.com
```

---

## Using Custom Ringtones

1. Go to the **Ringtones** tab
2. Click the upload zone (or drag and drop an audio file)
3. Supported formats: MP3, WAV, OGG — max **10 MB**
4. Hit the ▶ play button to preview before assigning
5. When adding or editing a birthday, pick your ringtone from the **Alarm Ringtone** dropdown
6. If signed in, ringtones are stored in Firebase Storage and sync across devices

Built-in tones (Default Beep, Gentle Chime, Classic Bell) are always available and require no upload.

---

## Data Structure

Each birthday entry stores:

| Field | Type | Description |
|---|---|---|
| `id` | string | Unique identifier |
| `name` | string | Contact's full name |
| `nickname` | string | Optional nickname |
| `dob` | string | Date of birth (YYYY-MM-DD) |
| `alarmTime` | string | Daily alarm time (HH:MM) |
| `ringtoneId` | string | ID of the selected ringtone |
| `reminders` | object | Flags for 7-day, 1-day, on-day alerts |
| `notes` | string | Free-text notes |
| `giftIdeas` | string[] | List of gift ideas |
| `giftHistory` | object[] | `{ year, gift }` entries |

---

## Firestore Data Layout

```
users/
  {uid}/
    birthdays/
      {birthdayId}   ← birthday document
    ringtones/
      {ringtoneId}   ← ringtone metadata (name, url, storagePath)
```

Ringtone audio files live in **Firebase Storage** at `ringtones/{uid}/{id}-{filename}`.

---

## Browser Support

Works in all modern browsers. Requires:
- ES6+ (Chrome 60+, Firefox 55+, Safari 11+, Edge 79+)
- Web Audio API (for built-in tones)
- File API (for ringtone uploads)

---

## Local vs Cloud Mode

| Feature | Local (no sign-in) | Cloud (signed in) |
|---|---|---|
| Add / edit birthdays | ✅ | ✅ |
| Alarms & reminders | ✅ | ✅ |
| Upload ringtones | ✅ (session only) | ✅ (persisted) |
| Sync across devices | ❌ | ✅ |
| Export / Import JSON | ✅ | ✅ |

---

## File Size

Single self-contained HTML file. No npm, no bundler, no framework.

---

## License

MIT — use it, fork it, make it yours.
