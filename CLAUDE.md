# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A BigBlueButton plugin that provides a picture-in-picture (PiP) window showing webcams and screen sharing during video conferencing sessions. Uses the Document Picture-in-Picture API.

## Build Commands

```bash
# Install dependencies
npm ci

# Build for production (outputs to dist/PluginPictureInPicture.js)
npm run build-bundle

# Development server (runs on http://localhost:4701)
npm start

# Linting
npm run lint
npm run lint:fix
```

## Architecture

### Entry Points
- `src/index.tsx` - Plugin bootstrap, mounts MainComponent to DOM using uuid from script attribute
- `src/main/component.tsx` - Initializes BbbPluginSdk, creates PiP window via Document Picture-in-Picture API, sets up action button dropdown

### PiP Window Components (`src/plugin-pip/`)
- `component.tsx` - Main PiP container, handles presenter/viewer view modes and conditional layouts
- `components/cameras/` - Webcam video streams with talking indicator borders
- `components/screenshare/` - Screen sharing video display
- `components/actions/` - Control bar with audio, webcam, raised hands, and unread chat buttons
- `components/chat/` - Chat message notifications via toast system
- `components/ui/` - Shared UI components (toast, tooltip)

### Data Flow
Components use `bigbluebutton-html-plugin-sdk` hooks and GraphQL queries/mutations:
- `useCurrentUser()` - Current user state (presenter status)
- Custom hooks in `hooks.ts` files wrap GraphQL subscriptions for video streams, screenshare, raised hands, etc.
- Mutations in `mutations.ts` files handle actions like toggling audio/video

### Key Patterns
- Plugin state persisted to localStorage (`pip-plugin-active`)
- PiP window created via `documentPictureInPicture.requestWindow()`
- CSS styles injected directly into PiP window document
- View modes: `presenter-view` vs `viewer-view` with different layouts when screenshare is active

## SDK Integration

Uses `bigbluebutton-html-plugin-sdk` version ~0.0.95. For SDK development guidance, refer to https://github.com/bigbluebutton/bigbluebutton-html-plugin-sdk

## Deployment

The built `dist/PluginPictureInPicture.js` and `manifest.json` should be hosted on an HTTPS server. Register the plugin in BigBlueButton via:
```
pluginManifests=[{"url":"<your-domain>/path/to/manifest.json"}]
```

## Resources

For more information on developing and improving BBB plugins, see the official documentation:
https://docs.bigbluebutton.org/plugins/
