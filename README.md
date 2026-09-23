# DAT Project Monitoring Dashboard

An internal web app for the Data Automation Team (DAT) to monitor main projects, sub-projects/workstreams, progress, deadlines, PICs, issues and challenges.

It's a single page (`index.html`) with no build step, hosted on GitHub Pages.

## Features

- **Dashboard.** Portfolio KPIs, a donut/bar status chart that filters the project summary, sub-projects due soon or overdue, a copyable portfolio summary, and a **Download backup** button.
- **Overall Gantt.** Main projects only, with date range, quick ranges, status and PIC filters.
- **One tab per project.** Extra tabs move into a searchable **More** menu. Projects can be archived, restored or deleted.
- **Project tab**
  - Summary header, sub-project status cards and chart.
  - Sub-project table with inline status and completion % editing.
  - Issues & challenges table.
  - Project Summary section: achievements, open issues, next actions and remarks.
- **Progress.** Automatic (average of sub-projects) or manual, set per project.
- **Delay logic.** The team's reported status is never overwritten. Missed deadlines show as a separate system deadline warning.

## Two ways to run it

| Mode | When | Where changes are saved |
|---|---|---|
| **Standalone** | `config.js` left as-is | Each person's own browser only |
| **Shared** | `config.js` filled in with a Firebase config | One shared database. Everyone with the link sees and edits the same data, with live updates |

## Sharing data with your team (Firebase, free, about 15 minutes)

Anyone with the link can view and edit, and no login is needed.

### 1. Create the database

1. Go to https://console.firebase.google.com and sign in with a Google account.
2. Click **Create a project**, name it (e.g. `dat-project-tracker`), and click through. Google Analytics isn't needed.
3. In the left menu, open **Build → Firestore Database** and click **Create database**.
4. Pick a location close to your team (e.g. `asia-southeast1 (Singapore)`), choose **Start in production mode**, and click **Create**.

### 2. Allow the app to read and write

1. In Firestore, open the **Rules** tab.
2. Delete everything there and paste the contents of `firestore.rules` from this repo.
3. Click **Publish**.

### 3. Connect the app

1. Click the **gear icon → Project settings**.
2. Under **Your apps**, click the **web icon `</>`**, give it a nickname, and click **Register app**. Leave Firebase Hosting unticked.
3. Copy the values from the `firebaseConfig` block shown (`apiKey`, `authDomain`, `projectId`, `storageBucket`, `messagingSenderId`, `appId`).
4. In this GitHub repo, open `config.js`, click the **pencil icon**, paste the values in, and click **Commit changes**.

### 4. Open the link

Wait 1–2 minutes for GitHub Pages to update, then open the site.

- **First open:** the sample projects load into the shared database automatically. After that, every change anyone makes is saved centrally and appears on everyone's screen within a second or two.
- **No yellow "Standalone mode" banner?** Then the app is connected to the shared database.

### Good to know

- **Anyone who has the link can edit or delete data.** Share it only with your team.
- **Download a backup regularly.** Use the **Download backup** button on the Dashboard.
- **The Firebase config isn't a password.** It's normal for it to be visible in the code. Access is controlled by the rules in step 2.
- **Free plan limits.** The Firebase free plan (Spark) allows 50,000 reads and 20,000 writes a day, far more than a team tracker needs.

## Data model

| Collection | Key fields |
|---|---|
| `mainProjects` | name, description, pic, startDate, dueDate, status, progressMode, manualProgress, objective, benefit, remarks, summary{achievements, nextActions, overallRemarks}, archived, createdDate, lastUpdated |
| `subProjects` | **projectId**, name, description, pic, startDate, dueDate, progress, status, issues, resolution, remarks, lastUpdated |
| `projectIssues` | **projectId**, subProjectId, description, dateRaised, pic, priority, status, resolution, targetDate, actualDate, lastUpdated |

Project progress, status counts, overdue warnings and all dashboard figures are **calculated**, never stored.

## Connecting a different database later

All reads and writes go through `Store.save`, `Store.remove` and `initData()` in `index.html`. To move to SharePoint, Dataverse or SQL, replace those. Nothing else in the UI needs to change.
