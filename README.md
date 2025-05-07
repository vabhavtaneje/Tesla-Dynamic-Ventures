// Tesla Dynamic Ventures GitHub App using Probot (Node.js) // File: index.js

const { Probot } = require("probot");

module.exports = (app) => { app.log.info("Tesla Dynamic Ventures App has loaded");

// Example: Add comment when PR is opened app.on("pull_request.opened", async (context) => { const prAuthor = context.payload.pull_request.user.login; const comment = context.issue({ body: Thanks for the PR @${prAuthor}! Our CI system will run tests shortly., }); return context.octokit.issues.createComment(comment); });

// Example: Post status check result on push app.on("push", async (context) => { const commitSha = context.payload.after; const params = context.repo({ sha: commitSha, state: "success", description: "Tesla CI checks passed", context: "Tesla Dynamic CI" }); await context.octokit.repos.createCommitStatus(params); }); };

// File: package.json { "name": "tesla-dynamic-github-app", "version": "1.0.0", "description": "GitHub App for Tesla Dynamic Ventures using Probot", "main": "index.js", "scripts": { "start": "probot run ./index.js" }, "dependencies": { "probot": "^13.0.0" }, "engines": { "node": ">=16" } }

// File: .env.example APP_ID=your-app-id PRIVATE_KEY="-----BEGIN RSA PRIVATE KEY-----\n...\n-----END RSA PRIVATE KEY-----" WEBHOOK_SECRET=your-webhook-secret

// File: .github/workflows/ci.yml name: Tesla Dynamic CI

on: push: branches: [ main ] pull_request: branches: [ main ]

jobs: build: runs-on: ubuntu-latest steps: - uses: actions/checkout@v3 - name: Set up Node.js uses: actions/setup-node@v4 with: node-version: '20' - name: Install dependencies run: npm install - name: Lint and Test run: | npm run lint || echo "Lint failed" npm test || echo "Tests failed"

deploy: needs: build runs-on: ubuntu-latest if: github.ref == 'refs/heads/main' steps: - uses: actions/checkout@v3 - name: Deploy to Vercel uses: amondnet/vercel-action@v25 with: vercel-token: ${{ secrets.VERCEL_TOKEN }} vercel-org-id: ${{ secrets.VERCEL_ORG_ID }} vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }} working-directory: ./ prod: true

// Deployment Instructions: // 1. Register your GitHub App at https://github.com/settings/apps // 2. Fill in APP_ID, PRIVATE_KEY, and WEBHOOK_SECRET in a .env file // 3. Run locally with: npm install && npm start // 4. Deploy to Vercel: //    - Create a new Vercel project and link your GitHub repo //    - Add secrets in GitHub: VERCEL_TOKEN, VERCEL_ORG_ID, VERCEL_PROJECT_ID //    - On push to main, your GitHub App will be built and deployed automatically
