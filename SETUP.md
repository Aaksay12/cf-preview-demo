# CF Preview Demo — Setup Guide

A bare-minimum static site wired to Cloudflare Pages for automatic PR preview URLs.  
Designed to be edited by Claude Code on mobile and tested via preview URLs.

---

## Stack

| Layer | Tool |
|---|---|
| Hosting | Cloudflare Pages |
| Source control | GitHub |
| CI / preview deploys | Cloudflare Pages GitHub App (built-in) |
| Editing agent | Claude Code (mobile) |

No build step. No framework. No CI YAML needed for deploys — Cloudflare handles it natively.

---

## One-time setup (~10 min)

### 1. Create the GitHub repo

```bash
# On desktop, or via GitHub mobile / github.com
# Create a new empty repo, e.g. "cf-preview-demo"
# Then push this scaffold:

git init
git remote add origin https://github.com/YOUR_USERNAME/cf-preview-demo.git
git add .
git commit -m "init: bare minimum CF Pages scaffold"
git push -u origin main
```

### 2. Connect to Cloudflare Pages

1. Go to [dash.cloudflare.com](https://dash.cloudflare.com) → **Workers & Pages** → **Create** → **Pages**
2. Click **Connect to Git** → authorise the GitHub App (installs `cloudflare-pages` on your account/org)
3. Select the `cf-preview-demo` repo
4. Build settings:
   - **Framework preset**: None
   - **Build command**: *(leave blank)*
   - **Build output directory**: `/` (or `.`)
5. Click **Save and Deploy**

That's it. Cloudflare now watches every push to `main` and every PR branch.

### 3. Verify it works

- Visit `https://cf-preview-demo.pages.dev` — you should see the page
- The badge in the top-left will say **production**

---

## Day-to-day workflow with Claude Code on mobile

### Make a change via Claude Code

```
Open Claude Code → point to this repo
Ask: "Change the heading on index.html to say 'Hello from PR #2', 
      push as a new branch called feat/test-heading, open a PR"
```

Claude Code will:
1. Edit `index.html`
2. Create a branch + commit
3. Push and open a PR on GitHub

### Get the preview URL

Cloudflare Pages automatically:
- Detects the new branch
- Deploys it within ~30 seconds
- Posts a comment on the PR with the preview URL

URL format: `https://<branch-slug>.cf-preview-demo.pages.dev`

### Test on mobile

Open the preview URL in Safari. The badge will show **preview** in amber.

### Merge when happy

Merge the PR on GitHub → Cloudflare redeploys `main` → preview environment is torn down.

---

## Cloudflare Pages limits (free tier)

| | Free |
|---|---|
| Builds per month | 500 |
| Preview deployments | Unlimited |
| Custom domains | Unlimited |
| Bandwidth | Unlimited |

---

## Prompts to give Claude Code

Some useful starting prompts once the repo is live:

**Simple edit:**
> "Update the paragraph text in index.html, push to a new branch `feat/copy-update`, open a PR."

**Add a new section:**
> "Add a `<section>` below the card with today's date rendered in large text. New branch `feat/date-section`, PR please."

**Test a breaking change:**
> "Remove the card border and change the background to white. Branch `experiment/light-theme`, PR."

Then grab the preview URL from the PR comment and test before merging.

---

## File structure

```
cf-preview-demo/
├── index.html                        # The entire app
├── .gitignore
├── .github/
│   └── pull_request_template.md      # Keeps PRs tidy
└── SETUP.md                          # This file
```

---

## Troubleshooting

**Preview URL not appearing on PR**  
→ Check Cloudflare Pages dashboard → your project → Deployments tab. If it failed, the build log is there.

**Badge still says "production" on preview**  
→ The preview URL detection uses `window.location.hostname`. Make sure the Pages project name matches what's in `index.html` (update the `startsWith` check if you renamed the project).

**Want to add a custom domain later**  
→ Cloudflare Pages → your project → Custom domains → add it. DNS is automatic if your domain is already on Cloudflare.
