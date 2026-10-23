# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Status

Pre-implementation: the repo contains only `PRD.md` (written in Korean), which is the source of truth for scope. No code, build, lint, or test commands exist yet. Remote `origin` is `https://github.com/by040723/day4-cafe.git`, default branch `main`.

## Planned architecture (from PRD.md)

Subway sandwich-combination site. Constraints that shape every change:

- **No npm / no build step.** One standalone HTML file per page (`index.html`, `recommend.html`, `menu.html`, `myrecipes.html`, `login.html`), opened directly in a browser. Dependencies, if any, come from CDN `<script>` tags.
- **Shared shell.** `index.html` is the frame: left menu + center content area. All pages must keep the same layout, colors, and font. Since there is no build step, shared CSS/layout is either duplicated or put in a plain shared file, so keep the pages consistent when editing any one of them.
- **Visuals are CSS/SVG shapes and emoji only**, no photos. `menu.html` is a game-like builder: stack bread, cheese, vegetables, sauce with a live preview, then save.
- **Backend is the user's Supabase project `day4-cafe`:** Supabase Auth for signup/login/logout, a `recipes` table (user_id, name, bread, cheese, vegetables, sauces, public flag, created_at), and RLS so users can only edit/delete their own rows. Public recipes feed the "other people's recipes" cards on `recommend.html`.
- **AI is a single Edge Function, `cafe-ai`**, used for recommendations on `recommend.html`. Do not add other functions.
- Saving and `myrecipes.html` require login; unauthenticated users are redirected to `login.html`.

Open decisions listed in PRD section 12 (public/private toggle, ingredient list scope, AI input style) should be settled with the user before implementing.

## Working agreements

- The user asked for no commits or pushes unless requested explicitly.
- Do not put the Supabase service-role key in any HTML file; only the public anon/publishable key belongs client-side.
