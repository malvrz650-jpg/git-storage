# GitHub Pages File Storage Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Turn the current public repository into a GitHub Pages-backed static file store with stable URLs under `files/`, a root landing page, and minimal repository documentation.

**Architecture:** The repository stays branch-driven and static. Public assets live under `files/`, `.nojekyll` disables Jekyll processing, `index.html` explains the repository purpose, and `README.md` documents the stable-path rule and resulting URL pattern.

**Tech Stack:** GitHub Pages, static HTML, inline CSS, Markdown, Git

---

## File Structure

Files to create:

- `.nojekyll` - disables Jekyll processing so Pages serves files directly
- `index.html` - small informational landing page for the repository root
- `README.md` - repository usage rules, URL shape, and update guidance
- `files/docs/.gitkeep` - keeps the docs content directory tracked before real files exist
- `files/images/.gitkeep` - keeps the images content directory tracked before real files exist
- `files/media/.gitkeep` - keeps the media content directory tracked before real files exist

Existing planning context:

- `docs/superpowers/specs/2026-06-13-github-pages-file-storage-design.md`

## Task 1: Create The Static File Storage Skeleton

**Files:**

- Create: `.nojekyll`
- Create: `files/docs/.gitkeep`
- Create: `files/images/.gitkeep`
- Create: `files/media/.gitkeep`

- [ ] **Step 1: Confirm the repository is still in the expected pre-implementation state**

Run:

```powershell
git status --short
```

Expected: only planning documents are tracked or staged; no unrelated working tree noise appears.

- [ ] **Step 2: Create the failing manual check**

Run:

```powershell
Test-Path '.nojekyll'; Test-Path 'files/docs/.gitkeep'; Test-Path 'files/images/.gitkeep'; Test-Path 'files/media/.gitkeep'
```

Expected: `False` for each path before creation.

- [ ] **Step 3: Create the minimal file-serving skeleton**

Create:

```text
.nojekyll
files/docs/.gitkeep
files/images/.gitkeep
files/media/.gitkeep
```

File contents:

- `.nojekyll`: empty file
- each `.gitkeep`: empty file

- [ ] **Step 4: Run the manual check to verify the skeleton exists**

Run:

```powershell
Test-Path '.nojekyll'; Test-Path 'files/docs/.gitkeep'; Test-Path 'files/images/.gitkeep'; Test-Path 'files/media/.gitkeep'
```

Expected: `True` for each path.

- [ ] **Step 5: Commit the skeleton**

Run:

```powershell
git add -- '.nojekyll' 'files/docs/.gitkeep' 'files/images/.gitkeep' 'files/media/.gitkeep'
git commit -m "chore: scaffold GitHub Pages file storage layout"
```

Expected: commit succeeds with the four new tracked files.

## Task 2: Add The Root Landing Page

**Files:**

- Create: `index.html`

- [ ] **Step 1: Create the failing manual check**

Run:

```powershell
Test-Path 'index.html'
```

Expected: `False` before the page is created.

- [ ] **Step 2: Write the minimal landing page**

Create `index.html` with:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Public File Storage</title>
    <style>
      :root {
        color-scheme: light;
        --bg: #f4f1ea;
        --panel: #fffdf8;
        --text: #1f2933;
        --muted: #52606d;
        --line: #d9d2c3;
        --accent: #7c5e10;
      }

      * { box-sizing: border-box; }

      body {
        margin: 0;
        min-height: 100vh;
        font: 16px/1.6 Georgia, "Times New Roman", serif;
        color: var(--text);
        background:
          radial-gradient(circle at top, rgba(124, 94, 16, 0.08), transparent 36%),
          linear-gradient(180deg, #f8f5ee 0%, var(--bg) 100%);
      }

      main {
        max-width: 760px;
        margin: 64px auto;
        padding: 0 24px;
      }

      section {
        background: var(--panel);
        border: 1px solid var(--line);
        border-radius: 18px;
        padding: 32px;
        box-shadow: 0 20px 40px rgba(31, 41, 51, 0.08);
      }

      h1 {
        margin: 0 0 16px;
        font-size: clamp(2rem, 4vw, 3.4rem);
        line-height: 1.1;
      }

      p {
        margin: 0 0 16px;
      }

      .note {
        margin-top: 24px;
        padding: 18px 20px;
        border-left: 4px solid var(--accent);
        background: rgba(124, 94, 16, 0.08);
        color: var(--muted);
      }

      code {
        font-family: "Courier New", monospace;
        font-size: 0.95em;
      }
    </style>
  </head>
  <body>
    <main>
      <section>
        <h1>Public File Storage</h1>
        <p>This repository is used as a public file store served through GitHub Pages.</p>
        <p>Files are published from stable paths under <code>files/</code>. Updating a file in the repository updates the content served from the same public URL after Pages redeploys.</p>
        <div class="note">
          Example URL pattern: <code>https://&lt;user&gt;.github.io/&lt;repo&gt;/files/docs/guide.pdf</code>
        </div>
      </section>
    </main>
  </body>
</html>
```

- [ ] **Step 3: Verify the page content exists**

Run:

```powershell
Select-String -Path 'index.html' -Pattern 'Public File Storage','files/','GitHub Pages'
```

Expected: all three strings are found in `index.html`.

- [ ] **Step 4: Commit the landing page**

Run:

```powershell
git add -- 'index.html'
git commit -m "feat: add GitHub Pages landing page"
```

Expected: commit succeeds with `index.html`.

## Task 3: Document Usage And URL Rules

**Files:**

- Create: `README.md`

- [ ] **Step 1: Create the failing manual check**

Run:

```powershell
Test-Path 'README.md'
```

Expected: `False` before the file exists.

- [ ] **Step 2: Write the minimal repository documentation**

Create `README.md` with:

```markdown
# Public File Storage

This repository is a public file store served through GitHub Pages.

## URL Pattern

Public files are served from stable paths under `files/`:

`https://<user>.github.io/<repo>/files/<category>/<filename>`

Examples:

- `https://<user>.github.io/<repo>/files/docs/guide.pdf`
- `https://<user>.github.io/<repo>/files/images/logo.png`

## Rules

- Put public files under `files/`.
- If a public URL must stay stable, replace the file at the same path instead of renaming or moving it.
- Prefer ASCII filenames without spaces.
- Deleting a file breaks its public URL.

## Updating Content

1. Replace or add files in `files/`.
2. Commit and push to `main`.
3. Wait for GitHub Pages to redeploy.
4. Reuse the same URL for replaced files.
```

- [ ] **Step 3: Verify the rules are documented**

Run:

```powershell
Select-String -Path 'README.md' -Pattern 'files/','stable','Commit and push to `main`'
```

Expected: all three phrases are found in `README.md`.

- [ ] **Step 4: Commit the documentation**

Run:

```powershell
git add -- 'README.md'
git commit -m "docs: add public file storage usage guide"
```

Expected: commit succeeds with `README.md`.

## Task 4: Final Repository Verification

**Files:**

- Verify: `.nojekyll`
- Verify: `index.html`
- Verify: `README.md`
- Verify: `files/docs/.gitkeep`
- Verify: `files/images/.gitkeep`
- Verify: `files/media/.gitkeep`

- [ ] **Step 1: Check the final file tree**

Run:

```powershell
Get-ChildItem -Recurse
```

Expected: the root contains `.nojekyll`, `index.html`, `README.md`, `docs/`, and `files/`, with the three tracked placeholder directories under `files/`.

- [ ] **Step 2: Check the working tree is clean**

Run:

```powershell
git status --short
```

Expected: no output.

- [ ] **Step 3: Review the final diff history**

Run:

```powershell
git log --oneline --decorate -5
```

Expected: recent commits include the scaffold, landing page, and README changes.

- [ ] **Step 4: Push and configure GitHub Pages manually**

Run:

```powershell
git push origin main
```

Expected: remote updates successfully.

Then in GitHub repository settings:

1. Open `Settings` -> `Pages`.
2. Set `Source` to `Deploy from a branch`.
3. Set `Branch` to `main` and folder to `/ (root)`.
4. Save and wait for deployment.

- [ ] **Step 5: Verify the deployed URLs manually**

Open in a browser after deployment:

- `https://<user>.github.io/<repo>/`
- `https://<user>.github.io/<repo>/files/...` for the first real file added to the repository

Expected:

- the root landing page loads with the explanatory content
- after at least one real file is added under `files/`, that file is served directly from its Pages URL
- replacing that file at the same path keeps the URL stable after redeploy
