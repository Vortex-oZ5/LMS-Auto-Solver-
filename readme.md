# LMS Auto-Solver (Vortex Auto-Pilot)

<p align="center">
  <strong>Autonomous AI-powered coding solver for Moodle LMS</strong><br>
  <em>Solves · Self-fixes · Submits — hands-free.</em>
</p>

<p align="center">
  <img alt="Version" src="https://img.shields.io/badge/version-6.5-blue?style=for-the-badge">
  <img alt="Platform" src="https://img.shields.io/badge/platform-Violentmonkey-E54A4A?style=for-the-badge">
  <img alt="AI Model" src="https://img.shields.io/badge/AI-Gemini-4285F4?style=for-the-badge&logo=google&logoColor=white">
  <img alt="Language" src="https://img.shields.io/badge/solves-Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white">
  <img alt="License" src="https://img.shields.io/badge/license-MIT-lightgrey?style=for-the-badge">
</p>

---

## Overview

**Vortex Auto-Pilot** is a Violentmonkey userscript that fully automates solving Moodle coding questions using the Google Gemini API. It detects the problem, generates a Java solution, injects it into the editor, clicks **Check**, repairs failures automatically, advances through the quiz, and submits the attempt.

The script exposes a floating debug panel (**`Alt + X`**) with live logs and settings.

---

## Table of Contents

- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [API Key Setup](#-api-key-setup)
- [Configuration](#️-configuration)
- [Running the Script](#️-running-the-script)
- [State Machine Reference](#-state-machine-reference)
- [Troubleshooting](#-troubleshooting)
- [Technical Reference](#-technical-reference)
- [Disclaimer](#️-disclaimer)
- [License](#-license)

---

## Features

| | |
|---|---|
| **Autonomous execution** | End-to-end: solve → check → self-fix → advance → submit. |
| **Self-healing** | Parses LMS error output and re-prompts Gemini to repair its own code. |
| **Multi-editor support** | Native support for Ace Editor, CodeMirror, and plain `<textarea>`. |
| **Model fallback chain** | Cycles through multiple Gemini models if any single one fails. |
| **Persistent state** | Completion state survives page reloads via `sessionStorage`. |
| **Settings panel** | In-page API key management with connection test. |
| **Rich text extraction** | Captures formula text from image `alt` attributes in question blocks. |
| **Auto-reset** | Detects new attempt pages and restarts cleanly. |

---

## Prerequisites

| Requirement | Notes |
|---|---|
| Modern browser | Chrome, Edge, Firefox, Brave, Opera, or Chromium-based browsers |
| **Violentmonkey** extension | Required to execute the userscript |
| Internet connection | Needed for Gemini API calls |
| **Google Gemini API key** | Free tier available — see [API Key Setup](#-api-key-setup) |
| Access to the LMS | Script matches `*://*/*`; designed for Moodle |

---

## Installation

### Step 1 — Install Violentmonkey

| Browser | Source |
|---|---|
| Chrome | [Chrome Web Store — Violentmonkey](https://chrome.google.com/webstore/detail/violentmonkey/jinjaccalgkegednnccohejagnlnfdag) |
| Edge | [Edge Add-ons — Violentmonkey](https://microsoftedge.microsoft.com/addons/detail/violentmonkey/eeagobfjdenkkddmbclomhiblgggliao) |
| Firefox | [Firefox Add-ons — Violentmonkey](https://addons.mozilla.org/firefox/addon/violentmonkey/) |
| Opera | [Opera Add-ons — Violentmonkey](https://addons.opera.com/extensions/details/violent-monkey/) |
| Other / manual | [violentmonkey.github.io](https://violentmonkey.github.io/get-it/) |

After installing, pin the **Violentmonkey** icon to your toolbar.

> Violentmonkey is open-source, has no analytics, and does not require a "Developer mode" toggle — one of its key advantages over other userscript managers.

### Step 2 — Create a New Userscript

1. Click the **Violentmonkey** toolbar icon.
2. In the popup, click the **“+” (Create a new script)** button at the top.
   - Alternative route: **Violentmonkey Dashboard → “+”** in the header.
3. A new editor tab opens with a small `// ==UserScript==` boilerplate.

### Step 3 — Paste the Script

1. Open `coding.js`.
2. Select everything and copy (`Ctrl + C` / `Cmd + C`).
3. In the Violentmonkey editor, **select all existing text** (`Ctrl + A`) and **paste** to replace it.
4. Save with **`Ctrl + S`** (`Cmd + S` on macOS).
5. The script appears in the dashboard with a green toggle — make sure it's **on**.

> The script header starts with `// ==UserScript==`. It must remain intact — it declares the `@match` and `@connect` directives that Violentmonkey uses for page injection and API permissions.

### Step 4 — Verify Installation

Open **Violentmonkey Dashboard → Installed** (or the popup) and confirm **LMS Auto-Solver (Vortex Auto-Pilot)** is listed with its toggle enabled.

### Step 5 — Grant Network Permission

Because the script calls `https://generativelanguage.googleapis.com`, Violentmonkey may show a permission prompt the first time it runs:

- Click **“Allow”** on the Violentmonkey confirmation panel that appears at the top of the page.

This is expected — the permission comes from the `@connect generativelanguage.googleapis.com` directive in the script header.

---

## API Key Setup

A valid Gemini key is required unless you're using the embedded default (not recommended — it may be rate-limited or revoked).

1. Visit **[aistudio.google.com/app/apikey](https://aistudio.google.com/app/apikey)**.
2. Sign in with a Google account.
3. Click **Create API key**.
4. Select or create a Google Cloud project.
5. **Copy** the generated key (starts with `AIza…`).

> Keep your key private. Do not commit it to public repositories.

---

## Configuration

### Option A — Configure via Panel (Recommended)

1. Navigate to any page.
2. Press **`Alt + X`** to open the panel.
3. Click the **Settings** icon in the top-right.
4. Paste your API key into the **Gemini API Key** field.
5. Click **Test Connection** — expect **"API is working"**.
6. Click **Save Key**.
7. Click **Back to Logs**.

The key is stored in `localStorage` under `vortex_api_key` and reused across sessions.

### Option B — Edit the Default Key (Not Recommended)

```js
const DEFAULT_API_KEY = "past_your_api";
