
### Prerequisites
Before starting, ensure you have the following set up:
- **Obsidian**: Download and install from [obsidian.md](https://obsidian.md/). This will be used for writing and managing your notes in Markdown format.
- **Node.js and npm**: Install Node.js (version 22 or higher) and npm (version 10.9.2 or higher) from [nodejs.org](https://nodejs.org/). Quartz requires these for building the site.
- **Git**: Install Git from [git-scm.com](https://git-scm.com/) if not already on your system.
- **GitHub Account**: Sign up or log in at [github.com](https://github.com/).
- **Cloudflare Account**: Sign up or log in at [cloudflare.com](https://cloudflare.com/). You'll need this for hosting.
- **Optional but Recommended**: Basic familiarity with command-line tools (terminal or command prompt) and Git version control.

### Step 1: Set Up Your GitHub Repository for Quartz
To keep everything in one GitHub repo (your notes, Quartz configuration, and build setup), start by forking the official Quartz repository. This allows easy updates from upstream while hosting your content.

1. Go to the official Quartz repository: [github.com/jackyzha0/quartz](https://github.com/jackyzha0/quartz).
2. Click **Fork** in the top-right corner to create a copy in your GitHub account. Name it something descriptive, like `my-digital-garden`.
3. Clone your forked repository to your local machine:
   ```
   git clone https://github.com/YOUR_USERNAME/my-digital-garden.git
   ```
   Replace `YOUR_USERNAME/my-digital-garden` with your actual repo details.
4. Navigate into the cloned directory:
   ```
   cd my-digital-garden
   ```
5. Install dependencies:
   ```
   npm install
   ```
6. Initialize Quartz with an empty content structure (this creates the `content/` folder where your notes will live):
   ```
   npx quartz create
   ```
   - When prompted, select "Empty Quartz" as the option.
   - Choose "Treat links as relative paths" for compatibility with Obsidian.

This sets up the repo with Quartz at the root and your notes in the `content/` subfolder. All changes will be managed in this single repo.

### Step 2: Integrate Your Obsidian Vault
Obsidian will use the `content/` folder as your vault, allowing you to write notes directly while keeping them synced via Git.

1. Open Obsidian and select **Open folder as vault**.
2. Navigate to and select the `content/` folder inside your cloned repo (e.g., `/path/to/my-digital-garden/content`).
3. Install the Obsidian Git plugin for seamless Git integration:
   - In Obsidian, go to **Settings > Community plugins > Browse**.
   - Search for "Git" (by denolehov) and install it.
   - Enable the plugin in **Settings > Community plugins**.
4. Configure the Git plugin:
   - Go to **Settings > Git**.
   - Set your Git username and email if not already configured globally (e.g., via `git config --global user.name "Your Name"` in terminal).
   - Enable auto-commit and auto-push features:
     - Turn on **Auto commit** and set an interval (e.g., every 5 minutes).
     - Enable **Auto push** to automatically push changes to GitHub after commits.
     - Optionally, enable **Auto pull on startup** to fetch remote changes when opening Obsidian.
5. Test the setup:
   - Create a test note in Obsidian (e.g., `test.md` with some content).
   - Use the Git plugin commands (via Command Palette, Ctrl/Cmd+P):
     - Search for "Git: Commit all changes" and run it (add a message like "Initial test commit").
     - Then "Git: Push" to upload to GitHub.
   - Verify the changes appear in your GitHub repo online.

This plugin automates committing and pushing from within Obsidian, making edits feel seamless without leaving the app.

### Step 3: Customize Quartz (Optional but Recommended)
Before publishing, tweak Quartz to fit your needs.

1. Edit the configuration file `quartz.config.ts` in the repo root:
   - Set `baseUrl` to your eventual site URL (e.g., `my-digital-garden.pages.dev` – you'll get this from Cloudflare later).
   - Customize themes, plugins, or layouts as per Quartz docs (e.g., add search functionality or custom CSS).
2. Preview locally:
   ```
   npx quartz build --serve
   ```
   Open [http://localhost:8080](http://localhost:8080) in your browser to see your site. Edit notes in Obsidian, rebuild with the command above, and refresh to preview changes.

### Step 4: Set Up Automated Deployment to Cloudflare Pages
Cloudflare Pages will automatically build and deploy your site from the GitHub repo on every push, creating a fully automated workflow.

1. Log in to your Cloudflare dashboard: [dash.cloudflare.com](https://dash.cloudflare.com/).
2. Select **Workers & Pages > Overview > Create application > Pages > Connect to Git**.
3. Authorize Cloudflare to access your GitHub account if prompted.
4. Select your Quartz repo (e.g., `my-digital-garden`).
5. In the **Set up builds and deployments** section:
   - **Project name**: Choose a name (e.g., `my-digital-garden` – this determines your initial URL like `my-digital-garden.pages.dev`).
   - **Production branch**: Set to `v4` (Quartz's default branch for version 4).
   - **Framework preset**: Select `None`.
   - **Build command**: Enter `npx quartz build`.
   - **Build output directory**: Enter `public`.
   - (Optional) If your notes rely on full Git history for timestamps, modify the build command to `git fetch --unshallow && npx quartz build`.
6. Click **Save and Deploy**. Cloudflare will build and deploy your site (takes about 1 minute).
7. Once deployed, access your site at the provided URL (e.g., `my-digital-garden.pages.dev`).
8. (Optional) Add a custom domain:
   - In Cloudflare Pages, go to **Custom domains > Add a domain**.
   - Follow the prompts to add your domain (e.g., `mynotes.com`) and update DNS records.

Now, every time you edit notes in Obsidian and push (manually or auto via the Git plugin), Cloudflare will detect the push, run the build, and update the live site automatically – no manual intervention needed.

### Step 5: Full Automated Workflow in Action
- **Editing**: Write/edit notes in Obsidian within the `content/` vault.
- **Syncing**: Use the Git plugin to auto-commit and auto-push changes at set intervals (or manually via commands).
- **Building & Deploying**: GitHub receives the push, triggering Cloudflare Pages to automatically rebuild the site with `npx quartz build` and deploy the static files from the `public/` folder.
- **Updates**: Changes go live in minutes. If you pull changes (e.g., from collaborators), Obsidian refreshes via the Git plugin.

### Troubleshooting Tips
- **Build Fails on Cloudflare**: Check logs in the Cloudflare dashboard. Common issues: Missing dependencies (ensure `npm install` ran locally first) or Git history (use the unshallow fetch command).
- **Obsidian Git Issues**: If auto-push fails, check authentication in the plugin settings. For large repos, commit in smaller batches.
- **Quartz Updates**: To pull updates from the official Quartz repo, add it as a remote: `git remote add upstream https://github.com/jackyzha0/quartz.git`, then `git pull upstream v4`.
- **Security**: Use GitHub personal access tokens if needed for authentication in the Git plugin.

This setup ensures a single repo handles your Obsidian notes and Quartz site, with end-to-end automation for publishing to Cloudflare Pages. If you encounter issues, refer to the official Quartz docs or Cloudflare support.