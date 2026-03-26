# Skills

Local skills for Claude or Codex, organized as small, reusable capability packs.

This repository is currently centered on a production-ready Playwright skill for browser automation: testing pages, validating flows, taking screenshots, exercising forms, and running ad hoc UI investigations without polluting the target project.

## What This Repo Is

Each skill lives in its own folder and ships its own instructions, runtime helpers, and setup.

The main user-facing skill here is:

- [`playwright-skill`](./playwright-skill): browser automation with Playwright, including dev-server auto-detection, a universal script runner, and reusable helper utilities.

Internal system skills are also present under [`.system`](./.system), but they are support infrastructure rather than the primary focus of this repository.

## Why It Exists

The goal is simple: make specialized workflows feel lightweight.

Instead of re-explaining a process every time, a skill packages:

- a clear operating guide in `SKILL.md`
- any runtime code needed to execute the workflow
- reusable helpers and references
- a structure that can be installed into a local Codex skills directory

## Featured Skill: Playwright

The Playwright skill is designed for real browser work, not just toy examples.

It provides:

- automatic localhost dev-server detection
- a universal executor that can run scripts from a file, inline code, or `stdin`
- helper utilities for launching browsers, authentication flows, screenshots, typing, clicking, scrolling, and page readiness
- a workflow that keeps temporary test scripts in `/tmp` instead of cluttering the repo under test
- headed execution by default for easier debugging

If you want to test a page, reproduce a UI bug, validate responsiveness, automate a login, or take screenshots for review, this is the skill you use.

## Repo Layout

```text
.
├── README.md
├── .system/
│   ├── skill-creator/
│   └── skill-installer/
└── playwright-skill/
    ├── SKILL.md
    ├── API_REFERENCE.md
    ├── run.js
    ├── lib/
    │   └── helpers.js
    └── package.json
```

## Quick Start

### 1. Install dependencies

```bash
cd playwright-skill
npm run setup
```

This installs the Playwright package and the Chromium browser used by default.

### 2. Read the skill instructions

Start with [`playwright-skill/SKILL.md`](./playwright-skill/SKILL.md). It defines the intended workflow, especially:

- detecting local dev servers first
- writing temporary scripts to `/tmp`
- running automation through the bundled executor

### 3. Execute automation

```bash
cd playwright-skill
node run.js /tmp/playwright-test.js
```

The executor also supports inline code and `stdin`:

```bash
cd playwright-skill
node run.js "await page.goto('https://example.com'); console.log(await page.title())"
```

```bash
cat /tmp/playwright-test.js | node playwright-skill/run.js
```

## Example Workflow

Detect a local app:

```bash
cd playwright-skill
node -e "require('./lib/helpers').detectDevServers().then(s => console.log(JSON.stringify(s, null, 2)))"
```

Then run a temporary Playwright script from `/tmp`:

```javascript
const { chromium } = require('playwright');

const TARGET_URL = 'http://localhost:3000';

(async () => {
  const browser = await chromium.launch({ headless: false });
  const page = await browser.newPage();

  await page.goto(TARGET_URL);
  console.log(await page.title());
  await page.screenshot({ path: '/tmp/homepage.png', fullPage: true });

  await browser.close();
})();
```

Execute it:

```bash
cd playwright-skill
node run.js /tmp/playwright-test.js
```

## Core Files

- [`playwright-skill/SKILL.md`](./playwright-skill/SKILL.md): workflow and usage instructions
- [`playwright-skill/run.js`](./playwright-skill/run.js): universal Playwright executor
- [`playwright-skill/lib/helpers.js`](./playwright-skill/lib/helpers.js): reusable automation helpers
- [`playwright-skill/API_REFERENCE.md`](./playwright-skill/API_REFERENCE.md): expanded Playwright patterns and reference material

## Design Principles

This repository favors a few pragmatic rules:

- keep skills self-contained
- make the happy path obvious
- avoid cluttering user projects with generated files
- prefer visible, debuggable automation over opaque background execution
- document workflows at the point of use

## Requirements

- Node.js `>=14`
- macOS, Linux, or another environment supported by Playwright
- network access when installing browsers and npm dependencies

## License

MIT. See the license field in [`playwright-skill/package.json`](./playwright-skill/package.json).
