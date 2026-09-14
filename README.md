# PSGEV — A local Powershell-based Launcher & Manager for God's Eye View 

A lightweight, all-PowerShell companion toolkit for [God's Eye View](https://github.com/bilawalsidhu/gods-eye-view) — the open-source photorealistic 3D OSINT globe. This toolkit wraps the official terminal-based install into a polished Windows experience: one-click install, a system-tray controller, key management, and clean uninstall — no Microsoft Store, no Docker, no dependencies beyond what it installs for itself.

## Features

- **One-script install** — clones the upstream repo, verifies Node.js 24.x/26.x and Git, installs them via Chocolatey or direct MSI if missing, runs the official setup doctor
- **System tray controller** — start/stop/restart the hidden background server, live status indicator, double-click to launch the app in your browser
- **API key management** — guided acquire-signups and enter-keys flows that write directly to the upstream `.env`, verified against the official doctor
- **Windows autostart options** — "Tray + Server" or "Tray only" modes via Startup-folder shortcuts (no registry edits, no scheduled services)
- **Clean reset & uninstall** — `.env` backup prompts, root-nuke of node_modules, scheduled-task cleanup

## Requirements

- Windows 10/11 (64-bit)
- Internet connection (offline build/install can be achieved with some edits to the install file)
- Administrator rights (for prerequisite installs only; daily use runs unelevated)
- Nothing else — Git, Node.js, and Chocolatey are auto-installed if missing

## Quick Start

```powershell
# 1. Download or clone this repo, then from its folder (elevated):
powershell -ExecutionPolicy Bypass -File .\Install-GEV.ps1

# 2. When asked, complete the interactive prompts — then launch:
.\GEV-Tools-Launcher.bat

## Using the Tray Controller

Once launched (via `GEV-Tools-Launcher.bat` or Windows autostart), a tray icon appears in your system tray. Everything operates from there — no terminals required for daily use.

### The Basics

- **Double-click the icon** — starts the server if it's stopped, or opens the app in your browser if it's running. This is the fastest path from tray to globe.
- **Hover the icon** — the tooltip shows live status (`God's Eye View - running` / `- stopped`), refreshed every 5 seconds.
- **Right-click the icon** — opens the full control menu below.

### Menu Reference

**Status line** *(top of menu, disabled)* — `● Server running` or `○ Server stopped`, auto-refreshed every 5 seconds.

**Server controls:**
| Item | Behavior |
|------|----------|
| ▶ Start server | Launches the app as a hidden background process (no console window); output appends to `gev-server.log` |
| ■ Stop server | Terminates all God's Eye View node processes for your install directory only — other node apps are untouched |
| ↻ Restart server | Stop + start; useful after editing `.env` so new keys take effect |
| Open in browser | Opens `http://localhost:4173` directly |

**API keys:**
| Item | Behavior |
|------|----------|
| Acquire API keys (signup portals) | Opens all five free-tier provider signup pages in browser tabs, in recommended priority order |
| Enter / update API keys | Interactive console prompts for each key; writes insert-or-replace into `.env` without disturbing other settings, re-applies owner-only file permissions, then runs the doctor to verify |
| Run setup doctor | Visible-console run of the upstream `npm run doctor` — shows which providers are live (`[OK]`) vs unconfigured (`[--]`) |
| Edit .env directly | Opens the raw config in Notepad for manual control |

**Start with Windows:**
| Item | Behavior |
|------|----------|
| Tray + GEV server | Startup-folder shortcut: tray loads AND server auto-starts at login |
| Tray only (no server) | Startup-folder shortcut: tray loads, server stays off until you want it |
| Disabled | Removes the autostart shortcut entirely |

Selecting any mode automatically removes the previous mode's shortcut — modes are mutually exclusive, no cleanup needed.

**Utilities:**
| Item | Behavior |
|------|----------|
|  View server log | Opens `gev-server.log` in Notepad — the hidden server's stdout/stderr |
|  View README | Opens this reference guide |
| ⚠ Reset ALL settings | Confirmation dialog, then replaces `.env` with the pristine `.env.example` template (stops the server first). Repo and node_modules are untouched — only keys/config are wiped |

**Exit** — If the server is running, asks whether to stop it too (Stop / Leave running / Cancel), then removes the tray icon and closes.

### Typical Session Patterns

**Casual browse:** double-click icon → globe opens. Close the browser tab when done; the server keeps running (low resource cost) or stop it via ■ Stop server.

**Config session:** right-click → 🔑 Enter API keys → paste your free-tier keys at the prompts → doctor confirms each flips to `[OK]` → ↻ Restart server → double-click to open. New layers appear immediately.

**Forgot to close it?** That's fine — if you set up `Setup-GEV-AutoStop.ps1`, the scheduled task kills the server after your configured time cap. Otherwise, the tray icon tooltip always shows whether it's running, and ■ Stop server is one click away.

### Notes on Behavior

- Helpers (key entry, doctor) run in a **visible console window** because they prompt for input — the tray continues running alongside them.
- The **server is hidden by design**: `Start server` never opens a console window. All its output goes to `gev-server.log`, viewable anytime from the menu.
- Stopping is **scope-safe**: only node.exe processes whose command line references your install directory are terminated.
- The tray is **single-purpose but not single-instance-unaware**: avoid launching two copies (two icons), as both will function independently.

## Tray Features at a Glance

-  **Live status** — menu header and icon tooltip track server state on a 5-second refresh
-  **Double-click intelligence** — start-if-stopped / open-if-running in one gesture
-  **Silent server management** — background process with rotating log capture, no console clutter
-  **Guided key workflow** — acquire → enter → verify (doctor) → restart, all from one menu
-  **Scope-safe shutdown** — process matching by install path, never touches unrelated node apps
-  **Portable paths** — everything resolves relative to the toolkit's own location (`%~dp0` / `$PSScriptRoot`); move the folder, it all still works
-  **Two autostart personalities** — always-on ("Tray + Server") or on-demand ("Tray only")
-  **Surgical reset** — wipes only `.env` (with confirm + template restore), never the repo or dependencies
-  **Self-documenting** — README and log viewers built into the menu
