# Innotek Work App — Trial (Stages 0–4)

## What is this?

A **single-file PWA** (Progressive Web App) for the Innotek team to:
- See today's station assignments (My Day)
- Submit daily self-reports (autosave drafts, submit when ready)
- Record free-text diary entries (with automatic parsing)
- Request leave (full form)
- Assign tasks to teammates (Slack-style)

**Offline-first:** All data saves to your browser's local storage instantly. When the backend is online, manual sync/refresh buttons push/pull data.

**Trial access:** Limited to ~10 team members, controlled by your App Control accounts (same as the main ERP).

---

## 🚀 Quick Start

### For end users (team members):

1. **Open the app:** 
   ```
   https://raw.githubusercontent.com/tejasunity/Innotek-ERP/main/work-web/index.html
   ```

2. **Sign in** with your App Control username + password
   - If this is your first login, you'll be prompted to set a new password
   - (Admin set an initial password; you change it to your own)

3. **Configure backend** (one-time setup):
   - Click ⚙️ (Settings) in the toolbar
   - Set **Backend URL** to your laptop's IP, e.g. `http://192.168.1.50:8000`
   - Your Employee ID (ask your admin if unsure)
   - Save

4. **Use the app:**
   - **📅 My Day:** See your assigned stations for today
   - **📝 Self-Report:** Fill out readings + remarks, autosave, then submit
   - **📓 Diary:** Write free text, parser extracts batch numbers & readings automatically
   - **🏖️ Leave:** Request leave (dates + reason)
   - **✋ Assign:** Give tasks to teammates
   - **Sync:** When backend is online, hit **📤 Sync to Backend** to send queued data

5. **Offline mode:** Even if backend is offline, you can keep working. Data is safe in your browser. Sync when ready.

---

### For admin (you):

1. **Create accounts in App Control** (already done):
   - Username: e.g., `lab1`, `lab2`, `lab3`, etc.
   - Initial password: something temporary (users change on first login)
   - Level: L1 or higher

2. **Run the FastAPI backend:**
   ```bash
   cd innotek-platform/backend
   python -m uvicorn app.main:app --host 0.0.0.0 --port 8000
   ```

3. **Tell team when backend is online/offline:**
   - "Backend is live, please sync your data" → they hit the Sync button
   - "Backend offline for maintenance" → they keep working offline

4. **Endpoints the web app calls:**
   - `POST /api/work/login` — validate username/password (returns employee_id, token, needs_password_change flag)
   - `POST /api/work/change-password` — update password on first login
   - `POST /api/work/report` — submit daily self-report
   - `POST /api/work/diary` — submit diary entry + parse
   - `GET /api/work/myday?employee_id=...` — get standing assignments + facts
   - `POST /api/work/assign` — create task assignment

---

## Architecture

**Frontend:**
- Single HTML file, ~1000 lines of vanilla JS/CSS
- No build step, no dependencies
- localStorage for all data (offline cache + persistent queue)
- Manual "Sync" button controls when data flows to backend
- Basic auth modal on first load

**Backend integration:**
- Calls your FastAPI at a configurable URL (default: `http://192.168.1.50:8000`)
- Uses JSON POST/GET (no CORS issues because we're on same network during trial)
- Sync queue stored locally, retried on next sync attempt if failed

**Watermark:** Red "TRIAL — LIMITED ACCESS" label always visible (can't be hidden).

---

## Features per stage

| Stage | Feature | Status |
|-------|---------|--------|
| **0** | Basic auth + level-sync | ✅ Calls `/api/work/login` for real validation |
| **1** | Standing assignments (My Day) | ✅ Shows stations + duration badges |
| **2** | Station facts feed | ✅ Placeholder (extensible for future data) |
| **3** | Self-report + autosave + submit | ✅ Upsert-safe (no duplicate rows), visible Save button |
| **3** | Compilation (supervision) | ⏳ Backend side only (not in this PWA) |
| **3.5** | Slack-style Assign/tasks | ✅ Full form + task list |
| **4** | Diary + deterministic parser | ✅ Matches batch#, pH, temp, moisture, etc. |
| **4** | QC bridge | ⏳ Backend endpoint only |
| **5** | Token Review | ⏳ Deferred (awaiting tokens README) |
| **6** | Android APK | ⏳ Skipped (web version is faster to iterate) |

---

## Settings

Click ⚙️ to open Settings panel:

- **Backend URL:** Where your FastAPI is running, e.g., `http://192.168.1.50:8000`
- **Employee ID:** Your unique ID in the system
- **Display Name:** How you appear in task assignments
- **Export Data:** Download all your local data as JSON (backup)
- **Clear Local Data:** Wipe everything from this browser (use if starting fresh)

---

## Troubleshooting

**"Connection error" on login?**
- Backend URL might be wrong. Check Settings ⚙️ → Backend URL
- Backend might be offline. Check with admin.
- Firewall might block. Try: `http://127.0.0.1:8000` if on same machine.

**"Sync failed"?**
- Backend URL not configured. Check Settings.
- Backend offline. Data stays safe in browser, retry later.

**"Password needs to be changed" prompt never goes away?**
- The backend `/api/work/change-password` endpoint isn't responding. Check backend logs.

**Lost data?**
- Check Settings → Export Data to see if it's still in local storage.
- Check browser's Developer Tools (F12) → Application → Local Storage.

---

## Sharing the link

**For team members:**
```
Direct link (always fresh):
https://raw.githubusercontent.com/tejasunity/Innotek-ERP/main/work-web/index.html

Or via GitHub repo:
https://github.com/tejasunity/Innotek-ERP/blob/main/work-web/index.html
```

**Bookmark it:**
- On desktop: Ctrl+D (Windows) or Cmd+D (Mac)
- On mobile: Add to home screen (iOS: Share → Add to Home Screen; Android: ⋮ → Install app)

---

## Development / Updates

The code is in this repo under `work-web/index.html`. To update:

1. Edit `work-web/index.html`
2. Commit: `git add work-web/index.html && git commit -m "..."`
3. Push: `git push origin main`
4. Users reload their browser (Ctrl+Shift+R to bypass cache)

**No build step, no deployment pipeline needed** — updates go live immediately.

---

## Next steps

1. **Backend endpoints:** Implement the 6 endpoints above in your FastAPI (innotek-platform/backend)
2. **Test cycle:** Run backend, have team sign in + fill forms, hit Sync, verify data in Postgres
3. **Iterate:** Watermark means it's clearly a trial. Add feedback/features based on team testing
4. **Feedback loop:** Ask team what works, what doesn't, what's missing

---

## Questions?

- **Tech:** Check the code in `work-web/index.html` — all functions are clearly named
- **Design:** Offline-first, manual sync, no auto-polling, no internet required
- **Accounts:** Use App Control (same system as main ERP)
- **Backend:** FastAPI endpoints are the glue

---

**Built:** August 2026 | **Trial phase:** Limited access, real accounts, controlled backend | **Status:** Ready for team testing
