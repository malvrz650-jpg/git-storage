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
