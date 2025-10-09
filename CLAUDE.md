# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

### Setup
```bash
# Enable package manager
corepack enable

# Install dependencies
pnpm install

# Switch database mode (choose between SQLite and PostgreSQL)
make switch-db-mode
```

### Development Server
```bash
# Start backend (automatically starts frontend too)
cd apps/nestjs-backend
pnpm dev

# For plugin development (optional)
pnpm build:packages
cd plugins
pnpm dev
```

### Database Operations
```bash
# Generate Prisma schemas for both SQLite and PostgreSQL
make gen-prisma-schema

# Create database migration
make db-migration

# Apply migrations and switch database
make switch-db-mode
```

### Testing
```bash
# Run all tests
pnpm g:test

# Unit tests only
pnpm g:test-unit

# E2E tests only
pnpm g:test-e2e

# Backend E2E tests
cd apps/nestjs-backend
pnpm test-e2e

# Run specific test file
pnpm test-e2e [test-file]
```

### Code Quality
```bash
# Lint all packages
pnpm g:lint

# Type check all packages
pnpm g:typecheck

# Build all packages
pnpm g:build

# Fix linting issues
pnpm g:fix-all-files
```

## Architecture Overview

### Monorepo Structure
- **apps/**: Main applications (AGPL 3.0)
  - `nestjs-backend`: NestJS backend API server with WebSocket support
  - `nextjs-app`: Next.js frontend application
- **packages/**: Shared libraries (MIT)
  - `core`: Shared business logic, types, and utilities
  - `db-main-prisma`: Database schema, migrations, and Prisma client
  - `sdk`: SDK for extensions and plugins
  - `ui-lib`: Reusable UI components
  - `common-i18n`: Internationalization resources
  - `openapi`: OpenAPI specifications and types
- **plugins/**: Custom plugins (AGPL 3.0)

### Key Technologies
- **Backend**: NestJS, Prisma ORM, WebSocket (ShareDB), Bull queues
- **Frontend**: Next.js, React, TailwindCSS, Zustand state management
- **Database**: PostgreSQL (production) / SQLite (development)
- **Package Manager**: pnpm with workspaces
- **Testing**: Vitest for unit tests and E2E tests

### Database Architecture
- Uses Prisma as ORM with dual schema support (SQLite/PostgreSQL)
- Template-based schema generation from `packages/db-main-prisma/prisma/template.prisma`
- Migration workflow supports both database types
- Real-time collaboration via ShareDB

### Frontend Architecture
- Next.js app with TypeScript
- Component library in `packages/ui-lib`
- State management with Zustand
- Internationalization with next-i18next
- Real-time updates via WebSocket connection

### Backend Architecture
- NestJS with modular structure
- WebSocket support for real-time collaboration
- Authentication with multiple providers (OAuth, JWT)
- File storage abstraction (S3, MinIO, local)
- AI integration with multiple providers

## Development Guidelines

### Port Configuration
- Backend API: 3000
- WebSocket (dev): 3001
- WebSocket (prod): 3000
- Plugin dev server: 3002

### Database Switching
The project supports both SQLite (development) and PostgreSQL (production). Use `make switch-db-mode` to switch between them, which will:
1. Update environment variables
2. Generate appropriate Prisma schema
3. Apply migrations
4. Start required services (PostgreSQL via Docker if selected)

### Testing Strategy
- E2E tests located in `apps/nestjs-backend/test/`
- Unit tests distributed across packages
- Use `pnpm pre-test-e2e` to seed test database
- IDE integration available for VSCode/Cursor with debug configurations

### Hot Reload Issues
If backend changes don't take effect during development:
1. Check for port conflicts: `lsof -i:3000`
2. Kill old processes: `kill -9 [pid]`
3. Restart with `pnpm dev`

### Commit Convention
Follow conventional commits format:
- feat: New feature
- fix: Bug fix
- docs: Documentation changes
- test: Adding/modifying tests
- refactor: Code refactoring
- style: CSS/styling changes
- chore: Build process/tools changes

### Common Workflows
1. **Adding new features**: Modify backend in `apps/nestjs-backend`, frontend in `apps/nextjs-app`, shared logic in `packages/core`
2. **Database changes**: Edit `packages/db-main-prisma/prisma/template.prisma`, then run migration workflow
3. **UI components**: Add to `packages/ui-lib` for reusability
4. **Plugin development**: Use the plugin development server and SDK from `packages/sdk`