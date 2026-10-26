# OpenLab — Supabase Accounts + Live AI Lab Partner

This is the existing OpenLab frontend with a local Node.js server, Supabase email/password authentication, profile sync, and a server-side OpenAI API endpoint. The frontend design and guided tour are retained.

## Requirements

- Node.js 20 or newer
- A Supabase project
- An OpenAI API key (API usage may incur charges; a ChatGPT subscription is separate)

## 1. Configure secrets

From this folder, copy `.env.example` to `.env`:

- macOS/Linux: `cp .env.example .env`
- Windows PowerShell: `Copy-Item .env.example .env`

Fill in:

- `SUPABASE_URL`: Project URL from Supabase Project Settings → API Keys
- `SUPABASE_PUBLISHABLE_KEY`: the publishable key from the same page (never use a service-role/secret key in the browser)
- `OPENAI_API_KEY`: your OpenAI API key (server-only; never paste it into HTML or share it)
- `OPENAI_MODEL`: a model available to your API project

Never upload or share `.env`. It is ignored by `.gitignore`.

## 2. Create the profile table

In Supabase Dashboard, open SQL Editor, create a new query, paste the contents of `supabase/schema.sql`, and run it. This creates `public.profiles` with Row Level Security and policies so authenticated users can access only their own profile row.

## 3. Configure email auth URLs

In Supabase → Authentication → URL Configuration:

- Set Site URL to `http://localhost:3000`
- Add `http://localhost:3000/**` to Redirect URLs if you want email confirmation redirects to return to local development.

Keep email confirmation enabled unless you deliberately choose another policy. With confirmation enabled, signup displays a message to check email before signing in.

## 4. Install and run

Open a terminal in this folder and run:

```sh
npm install
npm start
```

Then open `http://localhost:3000`. Do not double-click `public/index.html`; Supabase configuration and AI requests are served by the local server.

## What is connected

- Email/password signup and sign-in through Supabase Auth
- Session persistence via the Supabase client
- Profile metadata synced to `public.profiles` when a user is signed in
- Profile edits attempt to sync to the signed-in user's own row
- Sign-out from Profile & Settings
- `/api/assistant` sends lab context to the OpenAI Responses API without exposing the API key to browser JavaScript
- `/api/config` serves only the public Supabase project URL and publishable key

## Important limitations before public deployment

This is a local development starter, not a production-ready public service. Before deploying, add robust server-side authentication for the AI endpoint, persistent rate limiting, abuse/cost controls, monitoring, privacy disclosures, and deployment-specific redirect URLs. The current AI endpoint is intended for local testing and should not be exposed publicly with your personal API key. The local profile may contain optional age and grade information; use test data while developing.

Saved labs, flashcards, and most learning progress still use local storage. They have not all been migrated to the database yet. Profile data is the first account-synced feature.
