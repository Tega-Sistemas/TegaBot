# TegaBot AI Instructions

This document outlines the architecture, workflows, and conventions for the TegaBot project to guide AI coding agents.

## Project Overview
TegaBot is a hybrid application serving as both a Discord Bot and an Express Webhook receiver. It acts as an interface between a Discord server and an internal Java REST API (`suporte17Java`).

## Architecture & Core Components

### Entry Point
- **`src/index.ts`**: The core file that initializes both services:
  1. **Express Server**: Listens on `process.env.PORT`. Handles incoming webhooks (e.g., `/receivecall`) to post updates to Discord.
  2. **Discord Client**: Connects using `discord.js` v13. Listens for user commands to query the internal API.

### Service Integration
- **`src/service/api.ts`**: Configured `axios` instance pointing to the local backend (`http://localhost:8080/suporte17Java/rest/`).
- **`src/routes.ts`**: (Currently minimal) Exporting express router.

## Developer Workflows

### Setup & Run
- **Development**: `npm run dev` (Runs `nodemon src/index.ts`).
- **Build**: `npm run build` (Transpiles to `dist` using Babel).
- **Environment**: Create a `.env` file with:
  - `PORT`: (e.g., 3000)
  - `TOKEN`: Discord Bot Token.

### Dependencies
- **Discord.js**: Version 13 (Uses `Intents.FLAGS` syntax).
- **Express**: Used for incoming webhook handling.

## Code Patterns & Conventions

### Command Handling
- **Manual Parsing**: Commands are parsed directly within the `messageCreate` event in `src/index.ts`.
- **Prefix**: Defined in `src/config.json` (currently `!`).
- **Logic**: 
  ```typescript
  // Pattern used in index.ts
  if (command === config.prefix + "chamado") { ... }
  ```

### Cross-Component Communication
- **Webhook -> Discord**: The `/receivecall` endpoint receives POST data and sends an Embed to a **hardcoded channel ID** (`915427908821069875`).
- **Discord -> Internal API**: User commands trigger calls to `api.post('postDadosChamado', ...)` which returns support ticket details.

### Specific Implementation Details
- **Embed Colors**: Priority levels ('BAIXA', 'MEDIA', 'ALTA', 'CR') are mapped to specific hex colors inline.
- **Data Flow**: `Express POST /receivecall` -> `Discord Channel` (One-way notification).
- **Hardcoded Values**: Be aware of hardcoded Channel IDs and local API URLs. Preserve them unless refactoring for configuration is requested.
