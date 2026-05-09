# Overview

Personal portfolio website for Hatem El Shiekh, a Digital Marketing Leader based in Dubai, UAE. Premium gold-on-black consulting aesthetic (inspired by consulting.com): Playfair Display serif headlines with white→silver gradient, gold announcement bar, pill-shaped gold CTAs. Homepage sections: Hero, About, Blog preview, Impact Numbers, Speaking (with Seamless Digital 2025 event photo background), Credentials, FAQ accordion, Contact. Additional pages: `/freeconsultation` (AI chatbot), `/privacy`, `/blog/:slug` (individual blog posts), `/admin` (dashboard).

# User Preferences

Preferred communication style: Simple, everyday language.

# System Architecture

## Frontend
- **Framework**: React 18 with TypeScript, bundled with Vite
- **Routing**: Wouter (lightweight client-side router) — `/` (home), `/admin` (dashboard), `/freeconsultation` (AI chat), `/privacy`, `/blog/:slug` (individual post), 404
- **Styling**: Tailwind CSS with CSS variables for theming, dark theme by default. Brand: pure black bg, gold accent (`--gold-from: 38 90% 55%`, `--gold-to: 45 95% 70%`), Playfair Display display font
- **UI Components**: shadcn/ui (new-york style) built on Radix UI primitives, stored in `client/src/components/ui/`
- **Animations**: Framer Motion for scroll-triggered animations and entry effects
- **Smooth Scrolling**: react-scroll for navigation between page sections
- **Icons**: Lucide React and react-icons (for social media icons like LinkedIn, YouTube, Instagram)
- **Forms**: React Hook Form with Zod validation via @hookform/resolvers
- **Data Fetching**: TanStack React Query for server state management
- **Path aliases**: `@/` maps to `client/src/`, `@shared/` maps to `shared/`

## Design System
- **Display font**: Playfair Display (serif) — applied via `.font-display` class on hero names and section H2s
- **Body/UI font**: Space Grotesk + Inter
- **Silver gradient text**: `.text-gradient-silver` — white→#b8b8b8 via background-clip
- **Gold gradient text**: `.text-gradient-gold` — hsl(gold-from) → hsl(gold-to) via background-clip
- **Pill CTA**: `.btn-pill-gold` — rounded-full, gold gradient fill, soft glow shadow
- **Announcement bar**: `client/src/components/AnnouncementBar.tsx` — dismissible, reads `announcement_text` CMS key
- **Gold divider**: `.gold-divider` — 1px radial gold gradient line above footer

## Backend
- **Runtime**: Node.js with Express 5
- **Language**: TypeScript, executed via tsx
- **API Structure**: `POST /api/contact`, `POST /api/training-access`, `GET /api/content` (site content map), `GET /api/faqs` (active FAQs), `GET /api/blog` (published posts), `GET /api/blog/:slug` (single post), AI chat endpoints, image generation, admin CRUD endpoints (`/api/admin/*` with Bearer token auth — includes content, FAQs, blog posts, conversations, knowledge base)
- **File Upload**: `POST /api/admin/knowledge/upload` accepts PDF, DOCX, DOC, XLSX, XLS, TXT, CSV, MD files (max 10MB). Uses multer for upload, pdf-parse for PDFs, mammoth for Word docs, xlsx for spreadsheets. Large files auto-chunked into multiple knowledge entries.
- **AI Integration**: Gemini AI via Replit AI Integrations (`@google/genai`). Chat uses `gemini-2.5-flash` model with a MENA Growth Architect system prompt. Routes in `server/replit_integrations/chat/routes.ts`. Image generation in `server/replit_integrations/image/routes.ts`.
- **Email Notifications**: Resend SDK sends email to hatemelshiekho@gmail.com on new training registrations (subject: "Training Registrations"), showing all registered emails with the new one highlighted in green. Uses `RESEND_API_KEY` secret.
- **Route definitions**: Centralized in `shared/routes.ts` with Zod schemas for input validation and response types
- **Build**: esbuild for server bundling, Vite for client bundling, orchestrated by `script/build.ts`
- **Dev mode**: Vite dev server middleware is attached to the Express server with HMR support
- **Production**: Static files served from `dist/public/`, server bundle at `dist/index.cjs`

## Data Layer
- **Database**: PostgreSQL (required via `DATABASE_URL` environment variable)
- **ORM**: Drizzle ORM with `drizzle-zod` for schema-to-validation bridging
- **Schema location**: `shared/schema.ts` — shared between frontend and backend
- **Current tables**: `messages`, `training_emails`, `conversations`, `chat_messages`, `knowledge_entries`, `site_content` (key/value CMS), `faqs` (id, question, answer, orderIndex, isActive, createdAt), `blog_posts` (id, title, slug, excerpt, content, coverImage, publishedDate, isPublished, createdAt)
- **Admin Dashboard**: Password-protected `/admin` page (uses SESSION_SECRET). Tabs: **Website Content** (CMS fields including `announcement_text` for the top bar), **FAQ**, **Blog**, **Knowledge Base**, **Conversations**
- **SEO**: JSON-LD Person+WebSite schema in index.html, FAQPage schema injected dynamically when FAQs load, BlogPosting schema on each blog post page. Dynamic sitemap at `/sitemap.xml` (includes published blog posts). `/robots.txt`. OG image: `/hatem-hero.png`
- **Migrations**: Drizzle Kit with `db:push` command for schema synchronization
- **Storage pattern**: `IStorage` interface in `server/storage.ts` with `DatabaseStorage` implementation. `DEFAULT_CONTENT` array in `server/storage.ts` is the source of truth for CMS keys and their defaults.

## Shared Code
The `shared/` directory contains code used by both client and server:
- `schema.ts` — Drizzle table definitions and Zod insert schemas
- `routes.ts` — API route definitions with paths, methods, input schemas, and response schemas

# External Dependencies

- **PostgreSQL**: Required database, connection via `DATABASE_URL` environment variable. Uses `pg` (node-postgres) driver with connection pooling.
- **Google Fonts**: Inter, Space Grotesk, and Playfair Display loaded via Google Fonts CDN (preloaded in `client/index.html`)
- **External Images**: Profile image hosted on Supabase storage (`qtrypzzcjebvfcihiynt.supabase.co`)
- **Social Links**: LinkedIn, YouTube, and Instagram profiles linked in the UI (external URLs, no API integration)
- **Replit Plugins**: `@replit/vite-plugin-runtime-error-modal`, `@replit/vite-plugin-cartographer`, and `@replit/vite-plugin-dev-banner` used in development when running on Replit
