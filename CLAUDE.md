# CLAUDE.md
回答は日本語で。
This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a public transportation timetable system ("monster-timetable") designed for deployment on Raspberry Pi devices via balenaCloud. It consists of:

1. **API Server** (`/api`) - Hono-based server that scrapes and serves train/bus timetable data in JSON format
2. **Display App** (`/app`) - Integrated X11 server + Tauri application container for displaying timetable information
3. **X11 Service** (`/xeyes`) - Standalone X11 display service (reference implementation)

## Development Commands

### API Server (uses Bun runtime)
```bash
cd api
bun install                    # Install dependencies
bun run dev                    # Start development server with watch mode
bun run debug:dev              # Development with debug logging
bun run start                  # Production server
bun run build                  # Build for production (outputs to dist/)
bun run main.ts                # Generate static JSON files in dist/
```

### Display Application (Integrated X11 + Tauri)
```bash
cd app
bun install                    # Install dependencies
npm run dev                    # Frontend development server only
npm run tauri dev              # Full Tauri app development (local)
npm run build                  # TypeScript check + Vite build
npm run tauri build            # Build production binaries

# For balena deployment - uses Dockerfile.template
# Multi-stage build: Rust/Node.js build stage + minimal runtime stage
```

### Docker Deployment
```bash
docker-compose up -d           # Start all services
```

## Architecture

### API Server (`/api`)
- **Runtime**: Bun with Hono framework
- **Entry**: `app/server.ts`
- **Routes**: 
  - `/api/kintetsu` - Kintetsu train schedules
  - `/api/jr` - JR train schedules
  - `/api/kintetsu-bus` - Kintetsu bus schedules
  - `/api/all` - Combined data from all sources
  - `/api/cache/clear` - Cache management
- **Parsers**: Web scraping logic in `app/lib/parsers/`
- **Cache**: In-memory caching with 1-hour TTL by default
- **Data Sources**: Configured in `config.json`

### Display App (`/app`)
- **Architecture**: Single-container solution combining X11 server and Tauri application
- **Frontend**: React + TypeScript + Tailwind CSS
- **Backend**: Rust (Tauri)
- **X11 Server**: Integrated Xorg server with fbdev driver for Raspberry Pi
- **Config Files**: 
  - `src-tauri/tauri.conf.json` - Tauri configuration
  - `xorg.conf` - X11 server configuration
  - `start.sh` - Integrated startup script
- **Build**: Multi-stage Dockerfile.template with balena template variables
- **Runtime**: Automatic process monitoring and restart for both X11 and Tauri

### Key Features
- Real-time countdown timers for departures
- Support for multiple transit operators (Kintetsu trains/buses, JR)
- Calendar-based scheduling (different schedules for weekdays/weekends)
- Responsive UI optimized for kiosk/display use

## Testing & Type Checking

Currently, there are no explicit test scripts defined. For type checking:
- API: TypeScript configured but no strict mode
- App: `npm run build` includes TypeScript checking with strict mode

## Environment Variables

### API Server
- `DEBUG_MODE` - Enable debug logging
- `NODE_ENV` - Production/development mode

### Desktop App (Docker)
- `VITE_API_BASE_URL` - API server URL (default: http://api:3000)
- `DISPLAY` - X11 display number
- `ROTATE_DISPLAY` - Screen rotation (right/left/normal)
- `WINDOW_SIZE` - Window dimensions
- `ROTATE_DELAY` - Rotation delay in seconds

## balenaCloud Deployment

### Key Files
- `balena.yml` - balenaCloud application configuration for Raspberry Pi
- `docker-compose.yml` - Service orchestration (api + app)
- `app/Dockerfile.template` - balena template with %%BALENA_MACHINE_NAME%% variable

### X11 Architecture
- App container runs integrated X11 server on :0 display
- Uses fbdev driver for direct framebuffer access on Raspberry Pi
- Automatic device permission setup and process monitoring
- Reference implementation in `/xeyes` directory for troubleshooting

### Environment Variables
- `UDEV=1` - Enable udev for device access
- `DISPLAY=:0` - X11 display configuration
- `ROTATE_DISPLAY` / `WINDOW_SIZE` / `ROTATE_DELAY` - Display customization