# Real Estate Platform – Next.js + Sanity + Clerk

This is a modern real estate web application built with [Next.js](https://nextjs.org), [Sanity](https://www.sanity.io/), and [Clerk](https://clerk.com/) for authentication. The project features advanced property search, agent dashboards, saved listings, image galleries, and organization-based role management.

---

## Features

- 🏡 **Advanced Property Search**:
  - Filter by price, bedrooms, bathrooms, property type, location, square footage, amenities, open house, and more.
  - Real-time pagination and search.
- 📷 **Image Gallery**: Interactive, accessible galleries with lightbox and thumbnails for property images with LQIP blur placeholders.
- 👤 **Authentication & User Profiles**: Powered by Clerk, with support for social logins.
- 🗂️ **Agent & User Dashboards**:
  - Agents can manage their listings, leads, and profile.
  - Users can save favorite properties and contact agents.
- 🏢 **Team/Organization Support** (via Clerk Organizations): Enable multi-agent teams, RBAC, and org-based routing.
- 📊 **Analytics**: Track agent performance, listing status counts, lead management, and more.
- 💅 **Custom UI**: Built with [shadcn/ui](https://ui.shadcn.com), modern design, accessibility best practices, and responsive layouts.
- 🔒 **Secure API routes**: Next.js Server Actions, route protection, and RBAC.
- ⚡ **Optimized for Vercel deployment**: Fast builds, edge-ready.

---

## Local Development

1. **Clone the repo** and install dependencies:

   ```bash
   git clone https://github.com/YOUR-ORG/YOUR-REPO.git
   cd YOUR-REPO
   pnpm install
   # or
   npm install
   # or
   yarn install
   ```

2. **Copy environment variables**:

   ```bash
   cp .env.example .env.local
   # Then configure SANITY and CLERK variables as needed
   ```

3. **Start the local Sanity Studio** (in another terminal):

   ```bash
   cd studio
   npm run dev
   # or
   pnpm dev
   # or
   yarn dev
   ```

4. **Run the development server** (in the root):

   ```bash
   pnpm dev
   # or
   npm run dev
   # or
   yarn dev
   # or
   bun dev
   ```

5. Open [http://localhost:3000](http://localhost:3000) to view the app.
6. Studio can be accessed at [http://localhost:3333](http://localhost:3333) (if using default Sanity config).

---

## Customization

- **Fonts**: Uses [Geist](https://vercel.com/font) via [`next/font`](https://nextjs.org/docs/app/building-your-application/optimizing/fonts).
- **Theming & Design System**: Based on shadcn/ui with full Tailwind CSS customization.
- **Sanity Schemas**: `studio/schemas/` contains modular types for property, user, agent, organization, lead, and more. Extend as needed.

---

## Useful Scripts

- `pnpm dev` – start Next.js app
- `pnpm build` – build for production
- `pnpm lint` – lint code
- `pnpm format` – format files
- `cd studio && pnpm dev` – run Sanity Studio

---

## Tech Stack

- Next.js (App Router, Server Actions)
- Sanity CMS (v3, GROQ queries)
- Clerk (User/Org auth)
- shadcn/ui (Component system)
- Tailwind CSS
- Typescript
- Lucide Icons
- Vercel (Deploy Ready)

---

## More

- Update the code in `app/` or `components/` to customize UI/logic.
- [Sanity Studio](studio/README.md) is fully decoupled and highly extensible.
- Questions? See `CONTRIBUTING.md` or open an issue.

---

© 2026 – Real Estate SaaS Example

