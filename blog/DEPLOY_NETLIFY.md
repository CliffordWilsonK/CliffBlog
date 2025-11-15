# Deploying This Site to Netlify — Quick Reference

This repository is set up for Netlify builds using Hugo. The repository root contains `netlify.toml` which configures the build command and publish directory.

If you already pushed this repo to GitHub, follow the steps below to host and enable automatic publishing on Netlify.

1. Connect repository in Netlify (recommended)

- Go to: https://app.netlify.com/
- Sign in (or create an account) and click **Add new site → Import from Git**.
- Choose **GitHub**, authorize Netlify (or select the org/repo), and then pick this repository (`CliffBlog` or your repo name).
- Netlify will auto-detect `netlify.toml` and its build settings. If you prefer manual settings, use:

  - Build command: `hugo --gc --minify`
  - Publish directory: `public`
  - Branch: `master` (or your chosen branch)

- Click **Deploy site**. The first deploy will run and you can view build logs in the site dashboard.

2. Auto-publish on pushes

- By default Netlify enables continuous deployment for the connected branch — every push triggers a build and deploy.
- Confirm in the Netlify dashboard: **Site settings → Build & deploy → Deploy contexts**.

3. Environment variables / Hugo version

- If your site needs environment variables (API keys, etc.), add them in the Netlify UI under **Site settings → Build & deploy → Environment**.
- To pin the Hugo version, either set `HUGO_VERSION` in the `netlify.toml` (already present) or add `HUGO_VERSION` in the Netlify environment variables.

4. Manual deploy with Netlify CLI (optional)

Install the CLI and deploy the `public` folder directly from your machine:

```powershell
npm install -g netlify-cli
# Build the Hugo site locally
hugo --gc --minify
# Deploy the contents of `public` to the linked Netlify site (first run will ask to link)
netlify deploy --prod --dir=public
```

5. GitHub Actions alternative (optional)

- If you prefer building with GitHub Actions and then deploying to Netlify, you can use `netlify/actions/cli` or run the Netlify CLI in a workflow. Let me know and I can add a sample workflow file.

6. Troubleshooting tips

- If a deploy fails, open the site’s **Deploys** page and inspect the build logs for errors from Hugo or missing dependencies.
- Common fixes: set `HUGO_VERSION`, add missing env vars, or check that `public/` is the correct publish directory.

If you want, I can now:

- connect the repo to Netlify step-by-step (I’ll guide you through the UI), or
- add a GitHub Action workflow to build and deploy automatically, or
- configure a custom domain and HTTPS in Netlify.

Which would you like next?
