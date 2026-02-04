# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Webside is a web-based Smalltalk IDE built with React. It consists of:
- An API specification for interacting with Smalltalk backends (any dialect: Bee, Pharo, Dolphin, etc.)
- A React frontend IDE that consumes this API

The API is divided into **static** endpoints (browse/change code) and **dynamic** endpoints (evaluate expressions, debug, profile).

## Commands

```bash
# Development
npm start              # Start dev server on http://localhost:3000
npm test               # Run tests with Jest
npm run build          # Production build

# Electron desktop app
npm run electron-serve # Run dev server + Electron together
npm run electron-build # Build production Electron app
```

## Architecture

### Component Structure

```
src/
├── components/
│   ├── App.js              # Root with routing
│   ├── Login.js            # Backend connection screen
│   ├── IDE.js              # Main IDE container (manages tools, settings, shortcuts)
│   ├── Backend.js          # API client (axios wrapper for all backend calls)
│   ├── ToolContainer.js    # Tab-based tool management with drag-and-drop
│   ├── tools/              # IDE feature tools (28 components)
│   │   ├── SystemBrowser.js    # Class/refactoring browser
│   │   ├── MethodBrowser.js    # Senders/implementors/references
│   │   ├── Workspace.js        # Expression evaluation
│   │   ├── Inspector.js        # Object exploration
│   │   ├── Debugger.js         # Stack debugger
│   │   ├── TestRunner.js       # Test execution
│   │   ├── Profiler.js         # Flame graphs and rankings
│   │   └── ...
│   ├── parts/              # Reusable UI components (36 components)
│   │   ├── CodeEditor.js       # Primary code editor
│   │   ├── MonacoEditor.js     # Monaco wrapper
│   │   ├── CodeMirrorEditor.js # CodeMirror wrapper
│   │   ├── MethodList.js       # Method listing
│   │   ├── ClassTree.js        # Class hierarchy
│   │   └── ...
│   ├── dialogs/            # Modal dialogs with useDialog hook
│   └── layout/             # Titlebar, Sidebar, etc.
├── model/
│   ├── Settings.js         # User settings/preferences
│   ├── StChange.js         # Code change representation
│   ├── St*.js              # Smalltalk data models (Class, Method, Object, etc.)
│   └── ai/                 # AI assistant integration
└── SmalltalkLexer.js       # Lexical analysis for syntax highlighting
```

### Key Patterns

- **Backend.js**: All API calls go through this axios wrapper. Methods return promises for REST endpoints like `/code/classes`, `/evaluations`, `/debuggers`, etc.
- **Tools**: Each tool is a React component in `tools/`. They share common parts from `parts/` (code editors, lists, trees).
- **IDE.js**: Orchestrates everything - tool management, keyboard shortcuts, theme, dialogs, and settings.
- **Two code editors**: Monaco (primary) and CodeMirror are both supported. See `MonacoEditor.js` and `CodeMirrorEditor.js`.

### API Endpoints (Backend Implementation)

The Smalltalk backend must implement these REST endpoints:
- `/code/*` - Class/method browsing and changes
- `/evaluations/*` - Expression evaluation
- `/debuggers/*` - Debugger operations
- `/profilers/*` - Profiling
- `/test-runs/*` - Test execution
- `/objects/*` - Object inspection/pinning
- `/changes/*` - Code change history

Full API spec is in `docs/api/`.

## Tech Stack

- React 18.2 with functional and class components
- Material-UI v5 for UI components
- Monaco Editor + CodeMirror 6 for code editing
- Axios for HTTP, Socket.io for real-time
- Electron for desktop builds
