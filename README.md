# My Resume

A single-file HTML resume hosted on GitHub Pages.
**One edit → push → live site updated automatically.**

---

## Live URL

After setup, your resume will be at:
```
https://<your-github-username>.github.io/<repo-name>/
```

---

## Setup (one-time, ~5 minutes)

### 1. Create the repository

1. Go to [github.com/new](https://github.com/new).
2. Name it something like `resume` (the name becomes part of your URL).
3. Set it to **Public** (required for free GitHub Pages).
4. Click **Create repository**.

### 2. Push these files

```bash
git init
git add .
git commit -m "init: add resume"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

### 3. Enable GitHub Pages

1. In your repo, go to **Settings → Pages**.
2. Under **Source**, select **GitHub Actions**.
3. Save. The first deploy starts automatically.

Your resume is now live at the URL shown on the Pages settings screen.

---

## Day-to-day workflow

### Editing your resume

Open `index.html` in any editor (VS Code, Sublime, even the GitHub web editor) and update the HTML content. The file has clear comments marking each section.

### Publishing an update

```bash
git add index.html
git commit -m "update: add new role at Company X"
git push
```

GitHub Actions picks this up and your live site is updated in ~30 seconds.

### Editing directly on GitHub (no git required)

1. Click `index.html` in the repo.
2. Click the **pencil icon** (Edit).
3. Make your changes and click **Commit changes**.
4. The site deploys automatically.

---

## Exporting as PDF

1. Open your live URL (or open `index.html` locally in Chrome / Edge).
2. Press **Ctrl+P** (Windows/Linux) or **⌘+P** (Mac).
3. Set:
   - Destination: **Save as PDF**
   - Paper size: **A4**
   - Margins: **None**
   - ✅ Background graphics: **On**
4. Click **Save**.

> **Tip:** Chrome and Edge produce the most accurate PDFs. Firefox works too but may render fonts slightly differently.

---

## Customising the design

All styles are in the `<style>` block at the top of `index.html`.

| Variable | What it controls | Default |
|---|---|---|
| `--accent` | Headers, links, bullet dashes | `#2563EB` (blue) |
| `--dark` | Name, company names | `#0F172A` |
| `--sidebar-bg` | Left column background | `#F8FAFC` |

Change `--accent` to personalise the colour scheme instantly.

---

## File structure

```
resume/
├── index.html                  ← The entire resume (edit this)
├── .github/
│   └── workflows/
│       └── deploy.yml          ← Auto-deploy on push (don't touch)
└── README.md                   ← This file
```

---

## Optional: auto-generate PDF on every push

If you want a PDF to appear automatically in GitHub Releases after every push, add this job to `deploy.yml`:

```yaml
  generate-pdf:
    runs-on: ubuntu-latest
    needs: deploy
    steps:
      - uses: actions/checkout@v4

      - name: Install dependencies
        run: npm install -g @puppeteer/browsers && npx @puppeteer/browsers install chrome

      - name: Generate PDF
        run: |
          node -e "
          const puppeteer = require('puppeteer');
          (async () => {
            const browser = await puppeteer.launch({ args: ['--no-sandbox'] });
            const page = await browser.newPage();
            await page.goto('file://' + process.cwd() + '/index.html', { waitUntil: 'networkidle0' });
            await page.pdf({ path: 'resume.pdf', format: 'A4', printBackground: true, margin: { top: 0, right: 0, bottom: 0, left: 0 } });
            await browser.close();
          })();
          "

      - name: Upload PDF artifact
        uses: actions/upload-artifact@v4
        with:
          name: resume-pdf
          path: resume.pdf
```
