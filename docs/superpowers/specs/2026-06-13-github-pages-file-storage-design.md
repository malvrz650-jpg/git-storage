# GitHub Pages File Storage Design

Date: 2026-06-13

## Goal

Use the current public GitHub repository as a simple public file store served through GitHub Pages.

The system should:

- serve static files from stable public URLs
- update file contents when the repository is updated
- avoid dashboards, catalogs, generators, or dynamic behavior
- expose a small root landing page that explains the repository purpose

## Scope

In scope:

- GitHub Pages publishing from the repository
- stable file paths under a dedicated `files/` directory
- root `index.html` with light styling and repository-purpose copy
- `.nojekyll` to disable Jekyll processing
- documentation for how URLs should be managed

Out of scope:

- file discovery UI
- search, dashboards, or catalogs
- versioned URLs
- custom domain setup
- build tooling or JavaScript application code

## Requirements

### Functional

1. Files must be publicly accessible through GitHub Pages URLs.
2. A file replaced at the same repository path must remain available at the same public URL after deployment.
3. The root page must explain that the repository is used as public file storage.
4. The repository should remain usable with direct links only; no generated index or management UI is required.

### Non-functional

1. The structure must stay simple enough to maintain manually.
2. Public URLs should be predictable from repository paths.
3. Filenames should be safe for URLs and easy to keep stable.
4. The setup should avoid optional moving parts unless they directly support static file serving.

## Recommended Approach

Use GitHub Pages on the current public repository with content committed directly to the default branch. Store public assets under a single top-level `files/` directory and treat the repository path as the source of truth for the final URL.

This is preferred over a catalog or generated site because the user only needs stable static links and repository-driven updates. It is also preferred over Git LFS or Releases because the stated file sizes stay below the normal repository limits and the goal is stable path-based hosting rather than large binary distribution.

## Architecture

### Publishing model

- GitHub Pages publishes from the repository on GitHub
- `.nojekyll` is present in the repository root
- the deployment source is the `main` branch, root folder
- a normal `git push` triggers Pages to republish updated content

### Content layout

Public files live under a dedicated root folder:

```text
files/
  docs/
  images/
  media/
```

Example repository paths:

- `files/docs/guide.pdf`
- `files/images/logo.png`
- `files/media/intro.mp4`

Expected public URL shape for the current repository:

```text
https://<user>.github.io/<repo>/files/docs/guide.pdf
https://<user>.github.io/<repo>/files/images/logo.png
```

### Root landing page

Add a root `index.html` with:

- a short title
- a short explanation that the repository is a public file store
- a note that stable links are based on `files/` paths
- a few example URL patterns
- light inline CSS only

The root page is informational, not navigational. It should not try to enumerate files or act as a dashboard.

## URL Strategy

The approved behavior is stable URLs with replaceable content.

Rules:

1. A public file keeps the same URL as long as its repository path does not change.
2. Replacing a file means overwriting the file at the same path.
3. Renaming, moving, or deleting a file changes or breaks the public URL.
4. Public-facing filenames should use ASCII and avoid spaces to reduce URL encoding issues.

## Operational Model

### Adding a file

1. Place the file under the appropriate `files/` subdirectory.
2. Commit and push the change.
3. Wait for GitHub Pages deployment to complete.
4. Use the resulting Pages URL.

### Updating a file

1. Replace the file contents at the same path.
2. Commit and push the change.
3. Wait for GitHub Pages deployment to complete.
4. Reuse the same public URL.

Note: external caches or a local browser cache may briefly continue serving the older file revision after deployment. This does not change the URL contract, but it does affect validation immediately after an update.

### Removing a file

Deleting a file intentionally invalidates its public URL. This should be treated as a breaking change for any consumers of that link.

## Error Handling And Risks

### Expected failure modes

- wrong repository path produces a wrong public URL
- file rename or move breaks existing external links
- file deletion produces a missing resource
- deployment lag delays availability of the new content
- browser or CDN caching delays visibility of the updated file at the same URL

### Mitigations

- keep all public content under `files/`
- document the stable-path rule in `README.md`
- use predictable directory naming
- treat path changes as API changes for external consumers
- when validating a just-updated file, account for cache delay before treating the deployment as broken

## Verification

Manual verification is sufficient for this design.

Initial verification:

1. enable GitHub Pages for the repository
2. configure Pages to deploy from `main` / root
3. push known sample files and the root page
4. open the root Pages URL
5. open one or two known file URLs under `files/`

Ongoing verification:

1. after replacing a file, open the same URL and confirm the new content is served
2. verify only the changed public links for routine updates
3. if content appears stale immediately after deployment, rule out browser caching before assuming the update failed

No CI, test runner, or generated manifest is required for the approved scope.

## Implementation Outline

The implementation should create:

- `.nojekyll`
- `index.html`
- `README.md`
- `files/` with initial placeholder subdirectories as needed, using tracked placeholder files where Git requires them

The implementation should not create:

- a file indexer
- build scripts
- client-side JavaScript
- GitHub Actions for catalog generation

## Open Decisions Already Resolved

- GitHub Pages domain: use the default Pages domain, not a custom domain
- URL behavior: stable URLs with replaceable content
- UI scope: no dashboard or file browser
- root page: include a small styled informational page
