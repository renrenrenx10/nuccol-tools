# DangerMouse — Nuclear Demand Model

HTML/JS app hosted on GitHub Pages, reading and writing SharePoint lists via Microsoft Graph / SharePoint REST API with MSAL.js authentication.

---

## Files

```
index.html   — The entire app (single file, no build step needed)
README.md    — This file
```

---

## Deploying to GitHub Pages

1. Create a new GitHub repo (e.g. `dangermouse-app`)
2. Push `index.html` and `README.md` to the `main` branch
3. Go to **Settings → Pages → Source → Deploy from branch → main / root**
4. Your app will be live at `https://<your-username>.github.io/dangermouse-app/`

---

## Azure App Registration — Required Steps

Your app registration (`ec0a6806...`) needs two things configured before sign-in will work:

### 1. Add the Redirect URI

In Azure Portal → **App registrations** → your app → **Authentication**:

- Click **Add a platform → Single-page application (SPA)**
- Add your GitHub Pages URL as the redirect URI:
  ```
  https://<your-username>.github.io/dangermouse-app/
  ```
- Also add `http://localhost` if you want to test locally
- Tick **Access tokens** and **ID tokens** under Implicit grant (if not already)
- Save

### 2. Add SharePoint API Permissions

In Azure Portal → your app → **API permissions**:

- Click **Add a permission → SharePoint → Delegated permissions**
- Add: `AllSites.Read` and `AllSites.Write`  
  *(or more granular: `Sites.ReadWrite.All`)*
- Click **Grant admin consent** (requires an admin account)

### 3. Regenerate Your Client Secret

The client secret shared in chat should be regenerated:
- Azure Portal → your app → **Certificates & secrets → New client secret**
- The HTML app does NOT use the client secret (it uses delegated user auth) — so this is just good hygiene

---

## SharePoint List Columns Required

The app expects these columns on your SharePoint lists. If any are missing, add them in **List Settings → Create column**.

### `DM - Products`
| Column | Type |
|--------|------|
| Title | Single line (built-in) |
| MaterialType | Choice or Single line |
| ResourceType | Choice or Single line |
| Weight | Number |
| Unit | Single line |
| LeadTime | Number |
| LeadTimeUnit | Choice (Months / Quarters / Years) |
| ProductionRate | Number |
| Assumptions | Multiple lines |

### `DM - Developers`
| Column | Type |
|--------|------|
| Title | Single line (built-in) |
| Capacity | Number |
| Notes | Multiple lines |

### `DM - Dev Schedules`
| Column | Type |
|--------|------|
| Developer_ID | Single line |
| Product_ID | Single line |
| ScheduleYear | Number |
| Quarter | Choice (Q1 / Q2 / Q3 / Q4) |
| Amount_Absolute | Number |

### `ChartCache`
| Column | Type |
|--------|------|
| Title | Single line (built-in) |
| ChartData | Multiple lines (plain text) |

---

## Local Testing

Because MSAL uses redirect auth, you need to serve the file over HTTP (not `file://`):

```bash
# Python
python -m http.server 8080

# Node
npx serve .
```

Then visit `http://localhost:8080` and add `http://localhost:8080` as a redirect URI in Azure.

---

## Notes

- The app uses **delegated auth** — users sign in with their own Microsoft 365 account
- No server required — everything runs in the browser
- The client secret is NOT used in this app and should not be put in any client-side code
- The existing chart at `renrenrenx10.github.io/dangermouse-chart/` is still used for the "View Chart" button, writing to the `ChartCache` list as before
