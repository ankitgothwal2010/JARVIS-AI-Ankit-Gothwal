# JARVIS AI Admin Studio — setup

This is a source-code editor connected to GitHub through a Cloudflare Worker. Saving a file creates a commit in your repository; the existing `push` workflow then attempts to build a new debug APK. It does not magically invent arbitrary features: new features still require correct Kotlin/XML code.

## 1) Deploy the Worker
1. Open Cloudflare Dashboard → Workers & Pages → Create → Worker.
2. Create a Worker named `jarvis-admin-api`, open **Edit code**, replace the editor with `cloudflare-admin-worker/worker.js`, and deploy.
3. In Worker Settings → Variables and Secrets, add:
   - Secret `GITHUB_TOKEN`: a GitHub fine-grained personal access token limited to `ankitgothwal2010/JARVIS-AI-Ankit-Gothwal`, with **Contents: Read and write** permission. Keep it secret; never put it in HTML.
   - Secret `ADMIN_PASSWORD`: use a long, unique password that you do not use elsewhere.
   - Variable `GITHUB_OWNER`: `ankitgothwal2010`
   - Variable `GITHUB_REPO`: `JARVIS-AI-Ankit-Gothwal`
   - Variable `GITHUB_BRANCH`: `main` (change to `master` if that is your default branch).
   - Variable `ADMIN_ORIGIN`: the exact origin hosting `admin-panel/index.html`, e.g. `https://ankitgothwal2010.github.io` if hosted on GitHub Pages. Do not include a path or trailing slash.
4. Redeploy the Worker after setting variables/secrets.

## 2) Host the panel
You can put `admin-panel/index.html` on GitHub Pages, or open it locally in a browser. For the origin check to work reliably, host it at the origin set in `ADMIN_ORIGIN`. Enter the Worker URL and admin password in the panel and click Connect.

## 3) Edit and build
- Use file paths such as `app/src/main/java/com/ankit/jarvis/MainActivity.kt` or `app/src/main/res/layout/activity_main.xml`.
- Click Load file, edit, then Save file & trigger build. The Worker commits the file to GitHub. Your repository workflow triggers on push.
- Go to Actions, open the latest run, wait for green, then download the `JARVIS-AI-debug-apk` artifact.
- If the build fails, inspect the compile errors before installing the APK.

## Safety notes
- Anyone with the admin password can modify allowed source files. Do not share it.
- The panel intentionally does not let you edit workflow files, repository secrets, or arbitrary repository paths.
- A debug APK is for testing. A production release should be signed securely; never upload a keystore or signing password to this panel.
- If the token leaks, revoke it in GitHub immediately and create a new one.
