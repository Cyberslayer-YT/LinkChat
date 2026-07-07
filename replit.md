# LinkChat

A private, link-only real-time chat app — create a room, share the link, and chat or voice-call with anyone who has it. No accounts required.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `artifacts/linkchat/` — React + Vite frontend (home page, room page, emoji picker)
- `artifacts/api-server/src/socket.ts` — Socket.IO server, in-memory room + user store
- `artifacts/api-server/src/routes/rooms.ts` — REST endpoints for room create/get
- `lib/api-spec/openapi.yaml` — OpenAPI contract (source of truth)

## Architecture decisions

- **In-memory rooms**: No database needed — rooms and chat history live in server memory and expire 10 min after the last user leaves.
- **Socket.IO path proxied via `/socket.io`**: Added to the API server's `artifact.toml` paths so the shared proxy forwards WebSocket upgrades correctly.
- **WebRTC signaling is room-scoped**: Server verifies sender and target share the same room before relaying offer/answer/ICE messages.
- **Single-room per socket**: Re-joining a different room cleans up the previous room membership atomically.

## Product

- Create a private room with one click → get a unique link like `/room/abc123xyz`
- Share the link — anyone with it can join by choosing a username
- Live text chat with emoji support, Discord-style message grouping, auto-scroll
- WebRTC voice calls — peer-to-peer audio with mic toggle and visual indicators
- Online users list with voice-active status
- Copy invite link button

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

_Populate as you build — sharp edges, "always run X before Y" rules._

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
