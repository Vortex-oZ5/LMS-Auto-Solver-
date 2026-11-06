# LMS Auto-Solver (Vortex Auto-Pilot)

<p align="center">
  <strong>Autonomous AI-powered coding solver for Moodle LMS</strong><br>
  <em>Solves · Self-fixes · Submits — hands-free.</em>
</p>

<p align="center">
  <img alt="Version" src="https://img.shields.io/badge/version-6.5-blue?style=for-the-badge">
  <img alt="Platform" src="https://img.shields.io/badge/platform-Tampermonkey-00485B?style=for-the-badge&logo=tampermonkey&logoColor=white">
  <img alt="AI Model" src="https://img.shields.io/badge/AI-Gemini-4285F4?style=for-the-badge&logo=google&logoColor=white">
  <img alt="Language" src="https://img.shields.io/badge/solves-Java-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white">
  <img alt="License" src="https://img.shields.io/badge/license-MIT-lightgrey?style=for-the-badge">
</p>

---

## Overview

**Vortex Auto-Pilot** is a Tampermonkey userscript that fully automates solving Moodle coding questions using the Google Gemini API. It detects the problem, generates a Java solution, injects it into the editor, clicks **Check**, repairs failures automatically, advances through the quiz, and submits the attempt.

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
| Modern browser | Chrome, Edge, Firefox, Brave, Opera, or Safari |
| **Tampermonkey** extension | Required to execute the userscript |
| Internet connection | Needed for Gemini API calls |
| **Google Gemini API key** | Free tier available — see [API Key Setup](#-api-key-setup) |
| Access to the LMS | Script matches `*://*/*`; designed for Moodle |

---

## Installation

### Step 1 — Install Tampermonkey

| Browser | Source |
|---|---|
| Chrome / Edge / Brave / Opera | [Chrome Web Store](https://chrome.google.com/webstore/detail/tampermonkey/dhdgffkkebhmkfjojejmpbldmpobfkfo) |
| Firefox | [Firefox Add-ons](https://addons.mozilla.org/firefox/addon/tampermonkey/) |
| Safari | [App Store](https://apps.apple.com/app/tampermonkey/id1482490089) |

After installing, pin the Tampermonkey icon to your toolbar.

### Step 2 — Create a New Userscript

1. Click the **Tampermonkey** toolbar icon.
2. Select **Create a new script…** (or **Dashboard → `+`**).
3. Clear the default boilerplate.

### Step 3 — Paste the Script

1. Open `coding.js`.
2. Select everything and copy (`Ctrl + C` / `Cmd + C`).
3. Paste into the Tampermonkey editor.
4. Save with **`Ctrl + S`** (`Cmd + S` on macOS).

> The script header starts with `// ==UserScript==` — it must remain intact; it declares the `@match` and `@connect` directives.

### Step 4 — Verify Installation

Open **Dashboard → Installed Userscripts** and confirm **LMS Auto-Solver (Vortex Auto-Pilot)** is listed and toggled **on**.

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
const DEFAULT_API_KEY = "AQ.Ab8RN6K0C0U4QkJGc0iz6vl_VGwTplBHMeoNCvvG8_H4wKZb2A";