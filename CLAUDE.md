# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Helios Launcher - a modded Minecraft desktop launcher built with Electron. Handles automatic installation of Java, Forge/Fabric, mods, and game assets so users can join modded servers seamlessly. Based on [HeliosLauncher](https://github.com/dscalzi/HeliosLauncher) by Daniel Scalzi.

## Commands

```bash
npm start          # Run in development (launches Electron window)
npm run lint       # ESLint (no test suite exists)
npm run dist       # Build for current platform
npm run dist:win   # Build Windows (NSIS installer)
npm run dist:mac   # Build macOS (DMG, universal)
npm run dist:linux # Build Linux (AppImage)
```

Requires Node.js 22.x.x (see `.nvmrc`).

## Code Style (enforced by ESLint)

- **No semicolons**
- **Single quotes**
- **4-space indentation**
- **Windows line endings** (`\r\n`)
- **No `var`** - use `const`/`let`
- Files in `app/assets/js/scripts/` have `no-undef` and `no-unused-vars` disabled (they run in a shared renderer context with implicit globals)

## Architecture

### Electron Process Model

**Main process** (`index.js`): Window lifecycle, IPC handlers, auto-updater, Microsoft OAuth window management. Creates a frameless 980x552 window loading EJS templates.

**Renderer process** (`app/`): Single-page app using EJS templates + jQuery + vanilla JS. No bundler (Webpack/Vite) - scripts load directly in the Electron renderer.

### Key IPC Channels

| Channel | Direction | Purpose |
|---------|-----------|---------|
| `autoUpdateAction` / `autoUpdateNotification` | Bidirectional | Auto-updater lifecycle |
| `MSFT_AUTH_OPEN_LOGIN` / `MSFT_AUTH_REPLY_LOGIN` | Renderer <-> Main | Microsoft OAuth flow |
| `MSFT_AUTH_OPEN_LOGOUT` / `MSFT_AUTH_REPLY_LOGOUT` | Renderer <-> Main | Microsoft logout |
| `distributionIndexDone` | Preloader -> Main -> Renderer | Distribution config loaded |
| `TRASH_ITEM` | Renderer -> Main (handle) | OS-level file deletion |

IPC opcodes are defined in `app/assets/js/ipcconstants.js`.

### Core Modules (in `app/assets/js/`)

| Module | Purpose |
|--------|---------|
| `authmanager.js` | Microsoft OAuth 2.0 authentication via helios-core |
| `configmanager.js` | JSON-based user settings persistence (game memory, JVM args, accounts, selected server) |
| `processbuilder.js` | Constructs Minecraft launch command (classpath, JVM args, mod loader setup for Forge/Fabric/LiteLoader) |
| `distromanager.js` | Fetches and caches the distribution index (server list, mod definitions) from remote |
| `preloader.js` | IPC preload script - bridges main and renderer contexts |
| `langloader.js` | i18n using TOML files (`app/assets/lang/en_US.toml`) |
| `discordwrapper.js` | Discord Rich Presence integration |
| `dropinmodutil.js` | Drop-in mod file management |

### UI Scripts (in `app/assets/js/scripts/`)

These run in the renderer with shared global scope (hence relaxed ESLint rules):

| Script | Purpose |
|--------|---------|
| `landing.js` | Main view - game launch orchestration, Java discovery, asset downloading, Discord RPC |
| `settings.js` | Settings panel - account management, Java config, memory, mods, per-server settings |
| `uibinder.js` | View routing and fade transitions between screens |
| `uicore.js` | Auto-updater UI and core window setup |
| `overlay.js` | Modal/dialog overlay system |
| `login.js` / `loginOptions.js` | Authentication UI |
| `welcome.js` | First-launch experience |

### View System

Views are EJS templates in `app/`: `landing.ejs`, `settings.ejs`, `login.ejs`, `loginOptions.ejs`, `welcome.ejs`, `overlay.ejs`. Navigation is handled by `uibinder.js` with fade animations. `app.ejs` is the root template; `frame.ejs` provides the custom window titlebar.

### External Dependencies

- **helios-core** (~2.3.0): Core launcher library - Minecraft version management, mod system, Java discovery, server authentication
- **helios-distribution-types**: TypeScript type definitions for distribution config format
- **@electron/remote**: IPC bridge for renderer process access to main process APIs
- **electron-updater**: Auto-update via electron-builder publish

### Microsoft Authentication Flow

OAuth 2.0 via Azure AD (client ID in `ipcconstants.js`). Main process opens a dedicated BrowserWindow for the login flow, captures the auth code from the redirect URL, then exchanges tokens through helios-core's `MicrosoftAuth`. Refresh tokens persist; access tokens are masked in logs.

## Configuration

User config stored at platform-specific app data path (`.helioslauncher/config.json`). Managed entirely through `ConfigManager` with synchronous read/write.

Build/packaging configured in `electron-builder.yml`. ASAR packaging enabled with maximum compression.
