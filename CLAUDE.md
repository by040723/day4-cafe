# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Status

Pre-implementation: the repo contains only `PRD.md` (written in Korean), which is the source of truth for scope. No code, build, lint, or test commands exist yet. Remote `origin` is `https://github.com/by040723/day4-cafe.git`, default branch `main`.

## Planned architecture (from PRD.md)

Subway sandwich-combination site. Constraints that shape every change:

- **No npm / no build step.** One standalone HTML file per page (`index.html`, `recommend.html`, `menu.html`, `myrecipes.html`, `login.html`), opened directly in a browser. Dependencies, if any, come from CDN `<script>` tags.
- **Shared shell.** `index.html` is the frame: left menu + center content area. All pages must keep the same layout, colors, and font. Since there is no build step, shared CSS/layout is either duplicated or put in a plain shared file, so keep the pages consistent when editing any one of them.
- **Visuals are CSS/SVG shapes and emoji only**, no photos. `menu.html` is a game-like builder: stack bread, cheese, vegetables, sauce with a live preview, then save.
- **Backend is the user's Supabase project `day4-cafe`.** Data lives in `recipes` (user_id, name, menu, bread, cheese, vegetables, sauces, request, created_at) and `recommendations` (currently unused). `recipes` is publicly readable but not directly writable: anon has SELECT only, and creating/editing/deleting goes through the `create_recipe` / `update_recipe` / `delete_recipe` SQL functions, which check a login token and only touch rows owned by that user. `recommendations` is still open to anon for read and write.
- **Login is a plain ID + password with no email or other personal data.** It runs on `signup_user` / `login_user` / `logout_user` over private `app_users` and `app_sessions` tables (bcrypt hashes, 5-miss lockout, 30-day token sessions stored as hashes), not Supabase Auth, which needs an email. The browser keeps `{user_id, username, token}` in `localStorage.sw_auth`. Pages send `p_token` to the write functions; the user id alone grants nothing.
- **AI is a single Edge Function, `cafe-ai`**, used for recommendations on `recommend.html`. Do not add other functions.
- Login is required for personal features. Logged out, `recommend.html` only lets visitors browse "다른 사람들의 조합" (the AI box and 추천 카드 show a login prompt, and saving asks for login); `myrecipes.html` shows a "log in first" prompt; saving from `menu.html` asks the user to log in first. The 추천 카드 are the user's own recipes copied from others' cards (`recipes.source_id`), and the section says "비어 있어요" with a nudge to browse others when there are none.

Open decisions listed in PRD section 12 (public/private toggle, ingredient list scope, AI input style) should be settled with the user before implementing.

## Working agreements

- The user asked for no commits or pushes unless requested explicitly.
- Do not put the Supabase service-role key in any HTML file; only the public anon/publishable key belongs client-side.
