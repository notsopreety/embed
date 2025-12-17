# HLS Video Player with Proxy

## Overview

This is an HLS (HTTP Live Streaming) video player application with a built-in proxy server. The application allows users to play HLS video streams by proxying requests through the server to handle CORS restrictions and add required headers (like Referer) for external video sources. It's designed to work with anime streaming sources, specifically handling M3U8 playlist files and video segments.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Runtime Environment
- **Bun**: The application uses Bun as its JavaScript/TypeScript runtime instead of Node.js, providing faster execution and native TypeScript support
- **Express 5**: Web framework for handling HTTP requests and routing

### Server Architecture
The server (`server.ts`) handles these responsibilities:

1. **Static File Serving**: Serves frontend files from the `public/` directory
2. **Proxy Endpoint** (`/proxy`): Fetches external HLS content while adding required headers (Referer, User-Agent) to bypass CORS and authentication requirements
3. **M3U8 Rewriting**: Processes HLS manifest files to rewrite URLs, routing all segment requests through the proxy
4. **Stream API** (`/api/stream?id=...`): Fetches both SUB and DUB versions of a stream in parallel and returns merged response
5. **Embed Route** (`/embed/:id`): Serves the embedded player for any video ID

### Frontend Architecture
- **Standalone HTML Pages**: Simple HTML files with embedded CSS and JavaScript
- **HLS.js Library**: Client-side HLS playback using the hls.js CDN library
- Two main views:
  - `index.html`: Main player interface with subtitle selection
  - `embed.html`: Professional embeddable player with full features

### Embedded Player Features (`/embed/:id`)
- **Custom Controls**: Play/pause, seek bar, volume slider, fullscreen, Picture-in-Picture
- **Auto-Hide Controllers**: Controllers automatically hide after 5 seconds of inactivity (keyboard, mouse, or click events), unless settings menu or subtitle settings modal is open
- **Quality Selector**: Switch between available resolutions (Auto, 1080p, 720p, 360p)
- **Subtitle Picker**: Multi-language subtitle support with on/off toggle
- **SUB/DUB Toggle**: Switch between subbed and dubbed audio tracks
- **Skip Buttons**: Skip intro and outro based on API timestamps
- **Mobile Support**: Double-tap left/right to seek ±10 seconds
- **Keyboard Shortcuts**: Space/K (play/pause), F (fullscreen), M (mute), J/L (±10s), arrows (±5s/volume), C (subtitles), P (PiP), 0-9 (seek to %)

### Proxy Strategy
The proxy rewrites M3U8 playlist files to route all video segment URLs through `/proxy?url=`. This approach:
- Solves CORS issues for cross-origin video sources
- Adds required Referer headers that some video hosts check
- Strips CODECS information from master playlists (compatibility fix)

### Deployment Configuration
- **Vercel**: Configured via `vercel.json` for serverless deployment
- Routes API calls (`/proxy`, `/embed`, `/api`) to the server
- Static assets served directly from the `public/` folder

## External Dependencies

### NPM Packages
- `express` (v5): HTTP server framework
- `hls.js` (via CDN): Client-side HLS video playback

### External Services
- **Video Sources**: Designed to proxy content from `vidwish.live` and `watching.onl` (anime streaming hosts)
- **Subtitle Sources**: Fetches VTT subtitle files from `mgstatics.xyz`

### Deployment Platform
- **Vercel**: Serverless deployment with `@vercel/node` for the server and `@vercel/static` for public assets