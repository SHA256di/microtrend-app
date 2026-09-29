# Aesthetic App

A web app where users take or upload a photo and get matched to a niche internet
aesthetic, with a summary of the trend, why they match, and two runner-ups.
Portfolio project: must be production-quality, mobile-first, and publicly usable.

## Stack
- Vite + React + TypeScript (strict mode)
- ESLint
- Vercel hosting; serverless function in `api/analyze.ts`
- Gemini via `@google/genai`, structured JSON output with an enum schema
- Aesthetic data lives in `src/data/aesthetics.json`

## Commands
- `npm run dev`: frontend only
- `vercel dev`: frontend + API locally
- `npm run lint`: must pass before every commit
- `npm run build`: must pass before every push

## Architecture
- Frontend resizes images client-side to max 1024px JPEG before upload (Vercel body limit ~4.5MB).
- `api/analyze.ts` sends the image + aesthetic cards to Gemini and returns
  `{ top: { id, confidence, why }, runners_up: [{ id, why }] }`.
- The model returns only IDs and the personalized "why". Names, summaries, and
  display content come from `aesthetics.json`.
- Shared types live in `src/types.ts` and are used by both frontend and API.

## Hard rules
- The model may only return aesthetic IDs from the enum in the response schema.
  Validate IDs server-side and drop anything invalid.
- Never store user photos anywhere. Feedback logs only aesthetic IDs and votes.
- The prompt must judge style only (clothing, colors, setting, objects, lighting).
  Never comment on bodies, weight, age, race, or attractiveness.
- The API key lives only in `.env` and Vercel env vars. Never hardcode it, never
  log it, never send it to the client.

## How we work
- Do ONE build step at a time, then stop and summarize what changed so I can test it.
- Don't add features, libraries, or files outside the current step without asking.
- Before committing: run lint and build, then `git status`. Confirm `.env` is not staged.
- Commit messages: imperative, describing what the commit does
  (e.g. "Add image upload and client-side resize").
- No `any` types. Functional components and hooks only.

## Build plan
- [x] 0. Scaffold Vite React TS app, push to GitHub
- [x] 1. Placeholder homepage deployed to Vercel
- [ ] 2. `aesthetics.json` with 15 cards (id, name, summary, visual_markers, not_to_confuse_with, source)
- [ ] 3. `api/analyze.ts` with Gemini + enum schema + server-side validation
- [ ] 4. Upload input (`accept="image/*"`), client-side resize, loading and error states
- [ ] 5. Results page: top match, 2 runner-ups, Aesthetics Wiki credit
- [ ] 6. Eval script over `eval/` images; tune confusing cards; record accuracy
- [ ] 7. Feedback buttons + "which should it have been?" + privacy note
- [ ] 8. (Optional) Shareable result card via html-to-image

Check off each step in this file when it's committed and deployed.
