# Personal-site cloud editing — implementation checklist

## Existing verified components
- GitHub Pages deploys from main.
- Decap Turbo publishes travel content into the GitHub repository.
- The collection editor and JSON files have been added on this feature branch, not main.

## Important: do not deploy the current feature branch as production yet
The legacy add/edit/delete controls in index.html still use localStorage.
The cloud read layer is a prototype and must be tested against the Decap CMS schema before merging.

## Secure architecture
- Keep GitHub Pages public and read-only for visitors.
- Create a separate authenticated write API (e.g. Cloudflare Worker protected with Cloudflare Access).
- Store a least-privilege GitHub credential as a Worker secret, never in index.html or browser storage.
- Verify the identity server-side on EVERY write, including uploads and checkbox changes.
- Restrict allowed repository, branch and file paths.
- Use GitHub Contents API with current SHA to avoid silent overwrites.
- Upload binary images to images/uploads; save their public paths in data/*.json.
- The browser should display a confirmed GitHub commit success before showing 'saved'.
- After writes, reload cloud JSON and reconcile GitHub Pages deployment delay.
- Preserve old localStorage until data migration has been verified.
- Protect against concurrent writes and stale revisions.

## Setup information required from the owner
1. Choose/create Cloudflare account and Worker.
2. Configure Cloudflare Access to authorize only the owner's email.
3. Provide the public Worker URL, Access application AUD and team domain (not secrets).
4. Create a fine-grained GitHub token with Contents read/write only for this repo; put it directly in Worker Secrets. Never send the token in chat.
5. Verify authenticated /health and rejected unauthorized writes.

## Before production
- Implement and test frontend login state and API client.
- Convert all save/delete/checkbox handlers to async authenticated writes.
- Implement safe image upload and migration.
- Test on another browser/device and after clearing site storage.
- Merge to main only after explicit verification.
