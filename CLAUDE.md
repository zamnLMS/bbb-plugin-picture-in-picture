# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A BigBlueButton plugin that provides a picture-in-picture (PiP) window showing webcams and screen sharing during video conferencing sessions. Uses the Document Picture-in-Picture API.

## Build Commands

```bash
# Install dependencies
npm ci

# Build for production (outputs to dist/BbbPluginPictureInPicture.js)
npm run build-bundle

# Development server (runs on http://localhost:4701)
npm start

# Linting
npm run lint
npm run lint:fix

# Unit tests (vitest)
npm run test:unit
npm run test:unit:coverage

# End-to-end tests (playwright, needs a running BBB)
npm test
```

## Architecture

### Entry Points
- `src/index.tsx` - Plugin bootstrap, mounts MainComponent to DOM using uuid from script attribute
- `src/main/component.tsx` - Initializes BbbPluginSdk, creates PiP window via Document Picture-in-Picture API, sets up action button dropdown

### PiP Window Components (`src/plugin-pip/`)
- `component.tsx` - Main PiP container, builds the stream grid
- `components/streams/` - Unified grid of webcams, screenshare and avatar tiles
  (`webcam-item.tsx`, `avatar-item.tsx`, `video.tsx`, grid/selector helpers in `utils.ts`)
- `components/actions/` - Control bar with audio, webcam, raised hands, and unread chat buttons
- `components/chat/` - Chat message notifications via toast system
- `components/raised-hands/` - Raised hands dropdown and lower-hand mutation
- `components/contexts/` - Layout and PiP window React contexts
- `components/warning/` - Focus warning shown when PiP activation fails
- `components/ui/` - Shared UI components (toast, tooltip)

### Tests (`tests/`)
- `tests/unit/` - Vitest unit tests, mirroring the `src/` tree
- `tests/structural/`, `tests/behavioral/` - Playwright e2e specs
- `tests/core/` - Shared Playwright helpers, fixtures and selectors

### Data Flow
Components use `bigbluebutton-html-plugin-sdk` hooks and GraphQL queries/mutations:
- `useCurrentUser()` - Current user state (presenter status)
- Custom hooks in `hooks.ts` files wrap GraphQL subscriptions for video streams, screenshare, raised hands, etc.
- Streams are read from the BBB page via DOM selectors, so `components/streams/hooks.ts` re-resolves them when tracks stop or the video list node is replaced
- Mutations in `mutations.ts` files handle actions like toggling audio/video

### Key Patterns
- Plugin state persisted to localStorage (`pip-plugin-active`)
- PiP window created via `documentPictureInPicture.requestWindow()`
- CSS styles injected directly into PiP window document
- Screenshare is the first tile in the grid; Focus/Unfocus makes it span 2 columns and 2 rows
- Participants without a camera get avatar tiles, and the grid is capped at `MAX_TILES`

## SDK Integration

Uses `bigbluebutton-html-plugin-sdk` version ~0.0.95. For SDK development guidance, refer to https://github.com/bigbluebutton/bigbluebutton-html-plugin-sdk

## Deployment

The built `dist/BbbPluginPictureInPicture.js` and `manifest.json` should be hosted on an HTTPS server. Register the plugin in BigBlueButton via:
```
pluginManifests=[{"url":"<your-domain>/path/to/manifest.json"}]
```

## Resources

For more information on developing and improving BBB plugins, see the official documentation:
https://docs.bigbluebutton.org/plugins/
