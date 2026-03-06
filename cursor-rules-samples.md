# 150+ Cursor Rules Mega Pack — Sample Files
## Vibe Coding Edition — 10 Real .cursorrules Examples

> These are genuine, production-quality .cursorrules files from the pack.
> Drop any file into your project root as `.cursorrules` and Cursor AI will follow these rules automatically.

---

## 1. Next.js 14/15 App Router + TypeScript + Tailwind + Shadcn/UI

```
# .cursorrules — Next.js 14/15 App Router
# Stack: Next.js 14+, TypeScript (strict), Tailwind CSS v3/v4, Shadcn/UI, Radix UI

## Project Architecture
- ALWAYS use App Router (/app directory). Never suggest Pages Router (/pages).
- Server Components are the default. Add "use client" ONLY when you need:
  - useState, useEffect, useReducer, or other React hooks
  - Browser-only APIs (window, document, localStorage)
  - Event listeners (onClick, onChange, etc.)
- Data fetching belongs in Server Components using native async/await fetch.
- Route Handlers live in app/api/**/route.ts — never use old pages/api/ pattern.

## TypeScript Rules
- Strict mode is ON. No implicit `any`. Every variable, param, and return type annotated.
- Use `interface` for object shapes, `type` for unions/intersections/mapped types.
- Place `interface Props` at the top of each component file, before the component.
- Use `satisfies` operator for config objects to get type checking without widening.
- Prefer `unknown` over `any` when type is truly unknown; narrow with type guards.

## Component Conventions
- Named exports only: `export function MyComponent()` — never default exports for components.
- One component per file. File name matches component name (PascalCase).
- Co-locate related hooks in the same file unless reused across 3+ components.
- Use Shadcn/UI primitives first. Only build custom if Shadcn doesn't cover it.
- All Tailwind — no CSS modules, no inline styles, no styled-components.
- Responsive-first: mobile layout first, then sm:, md:, lg: breakpoints.

## Data & State
- Zustand for global client state. React Query (TanStack) for server state caching.
- Zod schemas for all form validation and API input parsing. Never trust raw input.
- Use `useFormState` + `useFormStatus` for Server Action forms (no client fetch for forms).
- Database queries live in /lib/db/ — never inline SQL or ORM calls in components.

## Performance
- Use Next.js Image component for all images. Set width/height or fill prop.
- Dynamic imports with `next/dynamic` for components >50KB that aren't above the fold.
- Wrap Client Component trees with `<Suspense>` boundaries for streaming.
- Metadata API only — never use next/head in App Router.

## Error Handling
- Every async Server Component must have a co-located error.tsx boundary.
- Route Handlers return structured JSON errors: `{ error: string, code: string }`.
- Loading states use loading.tsx files, not conditional renders in the component.
```

---

## 2. FastAPI + Python + Pydantic v2 + PostgreSQL

```
# .cursorrules — FastAPI Backend
# Stack: FastAPI 0.110+, Python 3.11+, Pydantic v2, SQLAlchemy 2.0, PostgreSQL

## Project Structure
- Organize by feature, not by layer: /users/, /products/, /auth/ — each with router.py, schemas.py, models.py, service.py.
- Main app factory in app/main.py. Routers included via app.include_router().
- Settings loaded from environment via Pydantic BaseSettings in app/config.py.
- Never import from circular paths. Feature modules import from /core/ only.

## FastAPI Conventions
- All route handlers are async def. Never use sync def for endpoints.
- Use APIRouter with prefix and tags for every feature module.
- Dependency injection via Depends() for: DB sessions, current user, rate limiting, permissions.
- Request bodies use Pydantic BaseModel. Response models explicitly typed in @router.get(response_model=...).
- Never return raw dicts — always return a Pydantic model or JSONResponse.

## Pydantic v2 Rules
- Use model_validator (not @validator) for cross-field validation.
- Use field_validator with mode='before' or mode='after' as appropriate.
- Schema names: RequestName (input), ResponseName (output), DBName (ORM model).
- Use model_config = ConfigDict(from_attributes=True) for ORM models.
- Annotate Optional fields with `field: Type | None = None` syntax (not Optional[Type]).

## SQLAlchemy 2.0 Rules
- Use the 2.0 select() style: `stmt = select(User).where(User.id == user_id)`.
- Never use the legacy query API (session.query()).
- All models inherit from Base (declarative_base or DeclarativeBase).
- Async sessions only: AsyncSession from sqlalchemy.ext.asyncio.
- Use relationship() with lazy="selectin" for eager loading, never lazy loading in async.

## Authentication & Security
- JWT via python-jose. Access tokens expire in 15min, refresh tokens in 7d.
- Passwords hashed with bcrypt (passlib). Never store plaintext or MD5/SHA1.
- CORS configured explicitly — never use allow_origins=["*"] in production.
- Rate limiting via slowapi on sensitive endpoints (login, register, password reset).

## Error Handling
- Custom exception classes inherit from HTTPException with status_code and detail.
- Global exception handler registered in main.py for unhandled errors.
- Validation errors return 422 automatically — do not override this behavior.
- Log all 5xx errors with full traceback using structlog.
```

---

## 3. Bun + Hono + TypeScript (API Server)

```
# .cursorrules — Bun + Hono API
# Stack: Bun 1.x, Hono v4+, TypeScript strict, Zod, Drizzle ORM

## Runtime & Tooling
- This is a Bun project. Use Bun APIs (Bun.serve, Bun.file, Bun.env) — NOT Node.js APIs.
- Use bun:sqlite for local dev/testing. Production uses Drizzle ORM with PostgreSQL.
- Package manager is bun. Never suggest npm install or yarn add.
- All scripts in package.json use bun run, not node or ts-node.

## Hono Conventions
- App instance in src/app.ts. Routes in src/routes/**/*.ts. Exported as Hono instances.
- Use Hono's built-in Context typing: `(c: Context) => Response`.
- Middleware in src/middleware/. Register globally in app.ts or per-router.
- Use hono/zod-validator for all request body and query param validation.
- Response helpers: c.json(), c.text(), c.html() — never new Response() directly.
- Group related routes with `const route = new Hono()` and app.route('/prefix', route).

## TypeScript Rules
- Strict mode enabled. No `any` without justification comment.
- Define route types with Hono's type inference: `app.get('/', zValidator(...), async (c) => {...})`.
- Env variables typed via `Env` interface passed to new Hono<{ Bindings: Env }>().
- Use satisfies for config/option objects.

## Drizzle ORM
- Schema defined in src/db/schema.ts. One file per domain entity.
- Drizzle client in src/db/index.ts. Export as `db`.
- Migrations via drizzle-kit. Never write raw SQL migrations manually.
- Queries use Drizzle query builder — no raw sql`` unless absolutely necessary.
- Use db.transaction() for multi-step writes. Always handle rollback.

## Validation & Errors
- Zod schemas co-located with their route files.
- All error responses: `{ success: false, error: string, code?: string }`.
- Success responses: `{ success: true, data: T }`.
- HTTP status codes: 200 (success), 201 (created), 400 (bad input), 401 (unauth), 404 (not found), 500 (server error).
```

---

## 4. LangChain AI Agent + Python

```
# .cursorrules — LangChain AI Agent
# Stack: Python 3.11+, LangChain 0.2+, LangGraph, OpenAI/Anthropic, Pydantic v2

## LangChain Version Rules
- Use LangChain 0.2+ APIs (LCEL chains) — NOT deprecated AgentExecutor or LLMChain.
- Import from langchain_core, langchain_openai, langchain_anthropic — not the base langchain package for model calls.
- Use @tool decorator or StructuredTool.from_function() for all custom tools.
- All chains use LCEL pipe syntax: `chain = prompt | llm | output_parser`.

## Agent Architecture (LangGraph)
- Complex agents use LangGraph StateGraph — not legacy initialize_agent().
- State defined as TypedDict with Annotated fields using add_messages reducer.
- Nodes are pure functions: (state: AgentState) -> dict updates.
- Edges defined explicitly — avoid automatic routing unless simple linear flow.
- Checkpointing for long-running agents: use MemorySaver (dev) or PostgresSaver (prod).
- Human-in-the-loop: interrupt_before=["tool_call_node"] pattern for approval flows.

## Tool Development
- Every tool has: name (snake_case), description (clear, LLM-readable), args_schema (Pydantic BaseModel).
- Tools raise ToolException on expected failures — never raw exceptions.
- Async tools use @tool with coroutine — avoid sync blocking in async context.
- Tool descriptions must explain WHEN to use it, not just WHAT it does.

## LLM Configuration
- Temperature 0 for structured extraction/classification tasks.
- Temperature 0.7 for generation/creative tasks.
- Always bind tools to model: `llm_with_tools = llm.bind_tools(tools)`.
- Use structured output: `llm.with_structured_output(MyPydanticModel)` for typed responses.
- Set max_tokens explicitly for production — never rely on model defaults.

## Streaming
- All user-facing LLM calls stream: use astream() or astream_events().
- Yield tokens as Server-Sent Events in FastAPI: `StreamingResponse(event_generator())`.
- Buffer tool call chunks before executing — check chunk.tool_call_chunks accumulation.

## Memory & Context
- Conversation memory via LangGraph checkpointer — not deprecated ConversationBufferMemory.
- Thread-based isolation: every user session gets a unique thread_id.
- Trim message history > 20 turns using trim_messages() to avoid context overflow.
```

---

## 5. Rust + Axum + SQLx (Backend API)

```
# .cursorrules — Rust + Axum Web API
# Stack: Rust stable, Axum 0.7+, SQLx 0.7+, Tokio, Serde, Tower middleware

## Rust Style
- Edition 2021. Stable toolchain only — no nightly features unless explicitly enabled.
- Prefer `thiserror` for library errors, `anyhow` for application errors (main.rs, handlers).
- No unwrap() or expect() in production paths — propagate with ? operator.
- Use `tracing` crate (not log) for all logging. Structured fields: `tracing::info!(user_id = %id, "action")`.
- Clippy clean: run `cargo clippy -- -D warnings`. Fix all warnings before shipping.

## Axum Conventions
- App state in `AppState` struct, wrapped in `Arc<AppState>`. Passed via `.with_state()`.
- Extract state in handlers: `State(state): State<Arc<AppState>>`.
- Handler functions are async and return `impl IntoResponse`.
- Use `Json<T>` extractor for request bodies. `T` must implement `serde::Deserialize`.
- Respond with `(StatusCode, Json<ResponseBody>)` tuples for typed responses.
- Routes organized in modules: `routes/users.rs`, `routes/auth.rs` — exported as `Router`.
- Error type `AppError` implements `IntoResponse` — centralized error handling.

## SQLx Rules
- All queries use `sqlx::query_as!` macro for compile-time query checking.
- Database pool `PgPool` stored in `AppState`. Never create per-request connections.
- Migrations in `migrations/` directory, managed via `sqlx migrate run`.
- Transaction pattern: `let mut tx = pool.begin().await?;` — always commit or rollback.
- Use `#[derive(sqlx::FromRow)]` on all query result structs.

## Error Handling
- `AppError` enum with variants for NotFound, Unauthorized, Validation, Database, Internal.
- Each variant implements `IntoResponse` with appropriate HTTP status and JSON body.
- Database errors mapped to `AppError::Database` — never leak SQLx errors to clients.
- Input validation via `validator` crate — `#[derive(Validate)]` on request structs.

## Async & Performance
- Tokio multi-threaded runtime (`#[tokio::main]`).
- CPU-bound work in `tokio::task::spawn_blocking()` — never block async thread.
- Connection pool sizing: max_connections = num_cpus * 4 (default; tune per workload).
```

---

## 6. Supabase + Next.js (Full-Stack SaaS)

```
# .cursorrules — Supabase + Next.js Full-Stack
# Stack: Next.js 14 App Router, Supabase JS v2, TypeScript, Tailwind, Stripe

## Supabase Client Setup
- Two clients: server client (createServerClient from @supabase/ssr) and browser client (createBrowserClient).
- Server client in utils/supabase/server.ts — reads cookies via Next.js cookies() function.
- Browser client in utils/supabase/client.ts — singleton pattern with useMemo in components.
- NEVER use the service_role key client-side. Service role only in secure server routes.
- Row Level Security (RLS) enabled on ALL tables — no exceptions.

## Authentication Patterns
- Auth handled by Supabase Auth. Use createServerClient for server-side session reads.
- Protected routes: check session in layout.tsx or middleware.ts — redirect to /login if null.
- Auth callback route at /auth/callback/route.ts — exchanges code for session via exchangeCodeForSession.
- User metadata stored in profiles table (public.profiles) linked to auth.users via trigger.
- OAuth providers configured in Supabase dashboard — callback URL: /auth/callback.

## Database Conventions
- All tables have: id (uuid default gen_random_uuid()), created_at (timestamptz default now()), updated_at.
- RLS policies: SELECT for authenticated users matching auth.uid() = user_id.
- Use Supabase TypeScript codegen: `supabase gen types typescript --local > types/supabase.ts`.
- Query pattern: `const { data, error } = await supabase.from('table').select('*')`. Always handle error.
- Realtime subscriptions use supabase.channel() API — clean up with channel.unsubscribe() on unmount.

## Storage
- Buckets: public/ for user avatars/public assets, private/ for user documents (RLS protected).
- Upload pattern: `supabase.storage.from('bucket').upload(path, file, { upsert: true })`.
- Signed URLs for private files with short expiry (60 seconds for downloads).

## Stripe Integration
- Prices and products managed in Stripe dashboard — not hardcoded.
- Webhooks handled in /api/webhooks/stripe/route.ts — verify signature with stripe.webhooks.constructEvent.
- Customer/subscription data mirrored to Supabase: customers table, subscriptions table.
- Server Actions for checkout: create Stripe Checkout Session, return URL.
```

---

## 7. React Native + Expo (Mobile App)

```
# .cursorrules — React Native + Expo
# Stack: Expo SDK 51+, React Native, TypeScript, NativeWind, Zustand, React Query

## Expo & RN Fundamentals
- Expo SDK latest stable. Use Expo Router v3 for navigation (file-based, like Next.js).
- New Architecture enabled (Fabric + JSI). Avoid legacy bridge APIs.
- TypeScript strict mode. All component props typed with interface.
- Use NativeWind (Tailwind for RN) for all styling — no StyleSheet.create().
- Target iOS and Android equally. Test on both platforms before considering done.

## Expo Router Conventions
- Routes in /app directory. Use (tabs)/ for tab navigators, (stack)/ or implicit stack.
- Shared layouts in _layout.tsx files — one per navigation group.
- Dynamic routes: app/user/[id].tsx. Access params via useLocalSearchParams().
- Protected routes: redirect in layout using useEffect + router.replace() if not authed.
- Modals: use `<Stack.Screen options={{ presentation: 'modal' }}/>` pattern.

## Components
- Use React Native core components: View, Text, Pressable (not TouchableOpacity), ScrollView, FlatList.
- Never use TouchableOpacity — Pressable is the modern replacement with better feedback control.
- FlatList for all long lists (>10 items). Never render large lists with .map() in ScrollView.
- KeyboardAvoidingView + ScrollView pattern for all forms.
- SafeAreaProvider from react-native-safe-area-context wraps root layout.

## State Management
- Zustand for global state (auth, user preferences, cart, etc.).
- React Query (TanStack Query) for server data. Background refetch on app focus.
- AsyncStorage via @react-native-async-storage/async-storage for persistence — wrap with zustand-persist.
- Never store sensitive data (tokens) in AsyncStorage alone — use Expo SecureStore.

## Performance
- Use React.memo() for pure list item components.
- Image component from expo-image — not React Native's built-in Image.
- Avoid inline object/function creation in JSX (causes unnecessary re-renders).
- Use useCallback for event handlers passed to FlatList renderItem.
- Hermes engine enabled — verify with `__DEV__` checks in performance-critical paths.
```

---

## 8. Turborepo Monorepo (Full-Stack Workspace)

```
# .cursorrules — Turborepo Monorepo
# Stack: Turborepo 2.x, pnpm workspaces, TypeScript, shared packages

## Monorepo Structure
- Package manager: pnpm with workspaces. Never use npm or yarn in this repo.
- Workspace root: turbo.json (pipeline config), pnpm-workspace.yaml.
- Apps in /apps/* — runnable applications (web, mobile, api, admin).
- Packages in /packages/* — shared code (ui, config, utils, types, db).
- Each package has its own package.json with name: "@repo/package-name" convention.

## Package Conventions
- @repo/ui: Shared React component library. Components exported from index.ts.
- @repo/types: Shared TypeScript types/interfaces. No runtime code.
- @repo/config: Shared ESLint, TypeScript, Tailwind configs (eslint-config, typescript-config, tailwind-config).
- @repo/db: Drizzle ORM schema + client. Only package that talks to the database.
- @repo/utils: Pure utility functions. Zero framework dependencies.

## TypeScript Setup
- Root tsconfig.json with base settings. Each package extends @repo/config/typescript.
- All packages use composite: true for incremental builds.
- Strict mode in all packages. No exceptions.
- Path aliases in each app's tsconfig: `@/*` → `./src/*`.

## Turbo Pipeline
- build depends on ^build (upstream packages build first).
- lint and test run in parallel (no dependencies unless specified).
- dev runs all apps/packages in watch mode simultaneously.
- Cache inputs/outputs declared per task — never cache tasks with side effects.

## Cross-Package Imports
- Apps import from packages via workspace protocol: `"@repo/ui": "workspace:*"`.
- No circular dependencies between packages. Use `madge` to detect cycles.
- Types package can be imported by any package. DB package imported only by API apps.
- Never import from an app into a package — packages must be app-agnostic.
```

---

## 9. OpenAI / Anthropic SDK + TypeScript

```
# .cursorrules — OpenAI & Anthropic SDK Integration
# Stack: TypeScript, OpenAI SDK v4+, Anthropic SDK, Vercel AI SDK, streaming

## SDK Selection
- Use Vercel AI SDK (ai package) as the unified abstraction when targeting multiple providers.
- Use provider-specific SDK directly (openai, @anthropic-ai/sdk) for advanced features not in AI SDK.
- Never hardcode model names as raw strings — define MODEL constants in config/ai.ts.
- API keys loaded from environment only. Never in code. Never logged.

## OpenAI SDK v4 Patterns
- Client instantiation: `const openai = new OpenAI({ apiKey: process.env.OPENAI_API_KEY })`.
- Chat completions: `openai.chat.completions.create({ model, messages, stream: true })`.
- Streaming: iterate `for await (const chunk of stream)` — use `chunk.choices[0]?.delta?.content`.
- Structured output: use `response_format: { type: 'json_schema', json_schema: { schema } }` (not json_object).
- Function calling: use tools array with JSON Schema definitions. Handle tool_calls in message loop.
- Embeddings: `openai.embeddings.create({ model: 'text-embedding-3-small', input })`.

## Anthropic SDK Patterns
- Messages API: `anthropic.messages.create({ model, max_tokens, messages })`.
- System prompt as separate `system` parameter — not as a system role message.
- Streaming: `anthropic.messages.stream()` — use `.on('text', (text) => ...)` event handler.
- Tool use: define tools in `tools` array with input_schema (JSON Schema). Handle tool_use blocks.
- Cache control: `cache_control: { type: 'ephemeral' }` on long system prompts for cost savings.

## Prompt Engineering Rules
- System prompts define persona, constraints, output format — never put these in user messages.
- Few-shot examples belong in system prompt or as alternating human/assistant messages.
- For extraction tasks: specify exact JSON schema in prompt. Use structured output API.
- Token budgeting: estimate tokens before calls. Warn if context exceeds 80% of model limit.
- Temperature 0 for deterministic tasks (classification, extraction, code). 0.7 for generation.

## Cost & Safety
- Log every API call: model, input_tokens, output_tokens, latency, cost estimate.
- Implement retry with exponential backoff for rate limit (429) and server errors (500, 529).
- Content moderation: run openai.moderations.create() on user input before processing.
- Never pass raw user input directly to system prompt — sanitize and structure first.
```

---

## 10. Drizzle ORM + PostgreSQL (Database Layer)

```
# .cursorrules — Drizzle ORM + PostgreSQL
# Stack: Drizzle ORM 0.30+, PostgreSQL, TypeScript, node-postgres (pg) or Neon serverless

## Schema Design
- One file per domain entity in /db/schema/. All schemas exported from /db/schema/index.ts.
- Use Drizzle column helpers: pgTable, serial/uuid, text, varchar, integer, boolean, timestamp, jsonb.
- All tables have: id (uuid defaultRandom()), createdAt (timestamp defaultNow()), updatedAt (timestamp).
- updatedAt auto-updates via: `.$onUpdate(() => new Date())` on the column definition.
- Foreign keys declared inline: `userId: uuid('user_id').references(() => users.id, { onDelete: 'cascade' })`.

## Relations
- Declare relations using Drizzle relations() function for type-safe joins.
- One-to-many: `relations(posts, ({ many }) => ({ comments: many(comments) }))`.
- Many-to-many: junction table with explicit relations on both sides.
- Use db.query.tableName.findMany() API for relation-aware queries — not join builders.

## Query Patterns
- Prefer Drizzle's query builder over raw sql`` for type safety.
- Select specific columns: `.select({ id: table.id, name: table.name })` — never select *.
- Pagination: `.limit(pageSize).offset(page * pageSize)`.
- Search: use sql`...` with `ilike` for case-insensitive search on indexed columns.
- Bulk inserts: `db.insert(table).values([...records])` — batch in chunks of 1000 max.
- Upsert: `db.insert(table).values(data).onConflictDoUpdate({ target: table.id, set: { ...update } })`.

## Migrations
- `drizzle-kit generate` creates migration SQL files. Review before applying.
- `drizzle-kit migrate` applies to database. Run in CI/CD pipeline before app starts.
- Never edit generated migration files — modify schema and regenerate.
- Destructive migrations (drop column, change type): create with `--custom` flag and write safe migration.

## Connection Management
- Single PgPool instance, exported from /db/index.ts. Do not create per-request connections.
- Pool config: max: 20, idleTimeoutMillis: 30000, connectionTimeoutMillis: 2000.
- Serverless (Vercel/Neon): use neon() driver with `neonConfig.webSocketConstructor = ws` for cold starts.
- Always use transactions for multi-step writes: `await db.transaction(async (tx) => { ... })`.
- Handle DeadlockDetected and SerializationFailure errors with retry logic in transactions.
```

---

## Bonus: Go (Golang) + Chi Router

```
# .cursorrules — Go API Server
# Stack: Go 1.22+, Chi router, pgx v5, sqlc, Docker

## Go Style Rules
- Follow standard Go formatting: gofmt and goimports always applied.
- Error handling: never ignore errors. `if err != nil { return fmt.Errorf("context: %w", err) }`.
- Named return values only for short functions where it improves clarity — generally avoid.
- Context propagation: every function that does I/O takes `ctx context.Context` as first argument.
- Package names: short, lowercase, no underscores. File names: snake_case.go.
- Interfaces defined at point of use (consumer), not point of implementation.

## Chi Router Conventions
- Router setup in internal/server/routes.go. Sub-routers per feature.
- Middleware stack: requestID, logger, recoverer, cors, auth (in that order).
- Handler functions: `func (h *Handler) HandlerName(w http.ResponseWriter, r *http.Request)`.
- Response helper in internal/response/: `response.JSON(w, status, data)`, `response.Error(w, status, msg)`.
- Always read and discard request body: `defer r.Body.Close()`.

## sqlc Patterns
- SQL queries in /sql/queries/*.sql. Run sqlc generate to produce type-safe Go code.
- Never write raw SQL in Go files — all queries in .sql files.
- Transaction helper: `func WithTx(ctx, pool, fn)` wrapper for common pattern.
- Use pgx/v5 directly for complex scenarios sqlc can't handle.

## Project Layout
- cmd/api/main.go: entry point, DI wiring, server start.
- internal/: all application code (not importable by external packages).
- internal/domain/: business logic, pure functions, no dependencies.
- internal/store/: database access layer (generated by sqlc + hand-written repos).
- internal/handler/: HTTP handlers, thin layer over service.
- pkg/: truly reusable utilities (if needed — prefer internal/).
```

---

*Full pack includes 150+ files covering all the stacks above plus: SvelteKit, Remix, Elixir/Phoenix, GraphQL/Apollo, Vitest/Jest/Playwright testing setups, Docker/CI workflows, Terraform IaC, and more.*

*Purchase at: https://sportaholic000-hue.github.io/cursor-rules-pack/*
