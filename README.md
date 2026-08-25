# Innotek ERP WorkApp — Trial

Single-file web app (`work-web/index.html`) for a limited team trial. My Day, Self-Report,
Diary, Leave, Assign — all backed by the same App Control accounts as the main ERP.

## How it works

- **Offline-first:** every form saves to this browser's `localStorage` immediately, whether
  or not the backend is reachable.
- **Manual sync:** a queue of unsent items builds up locally; hit **📤 Sync** to push them to
  the backend, **⬇️ Refresh** to pull fresh assignments/leave status/tasks.
- **If the backend/server is off:** the app still opens and you can still fill in reports,
  diary entries, leave requests — they just wait in the queue. Sync/Refresh will show a clear
  error until the backend is back.

## Two ways to open it

1. **This GitHub Pages link** — reachable from anywhere, any device, any time:
   `https://tejasunity.github.io/Innotek-ERP/work-web/`
   Good for filling in data offline. Because this page is served over **https://**, a plain
   **http://** backend on the local network will be blocked by the browser for Sync/Refresh
   (mixed-content rule) — the data still saves locally, it just can't push/pull until the
   backend has a matching protocol.

2. **The backend's own address** — e.g. `http://192.168.1.50:8000/` when the FastAPI backend
   is running on that laptop and you're on the same WiFi. Same page, served from the same
   place the API lives, so Sync/Refresh work end-to-end with no extra setup. Use this one
   when you actually want to send data through during the trial.

## First login

Your admin issues a one-time password in App Control. Sign in with that — you'll be asked to
set your own right after. Usernames are matched case-insensitively.

## What's NOT in this trial

Lab/QC test entry and Dispatch/finished-goods release are not part of this web app yet — this
first pass covers station assignments, self-reporting, diary, leave, and task assignment only.
