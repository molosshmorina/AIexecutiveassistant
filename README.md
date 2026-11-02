# Personal Assistant (AI chief of staff)

Next.js 14 + Firebase (Google sign-in, email/password sign-up, Firestore) + Claude chat that saves meetings and commitments for you.

## Setup
1. **Firebase**: create a project at console.firebase.google.com.
   - Authentication > Sign-in method: enable **Google** and **Email/Password**.
   - Authentication > Settings > Authorized domains: add your production domain (localhost is allowed by default).
   - Firestore Database: create it, then paste `firestore.rules` into the Rules tab and publish.
   - Project settings > Your apps > Web app: copy the config into `.env.local` (`NEXT_PUBLIC_*`).
   - Project settings > Service accounts > Generate new private key: copy `project_id`, `client_email`, `private_key` into `FIREBASE_*`.
2. **Claude**: create an API key at console.anthropic.com and set `ANTHROPIC_API_KEY`. `ANTHROPIC_MODEL` defaults to `claude-sonnet-5-5`.
3. `cp .env.example .env.local`, fill it in, then `npm install && npm run dev` and open http://localhost:3000.
4. Deploy: Vercel works as is. Add the same env vars there.

## How it works
- `app/login`: Google SSO and account creation (email + password, with reset link).
- `app/(app)/*`: dashboard, chat, meetings, commitments. Data lives at `users/{uid}/items` in Firestore, readable only by its owner (see `firestore.rules`).
- `app/api/chat/route.js`: verifies the Firebase ID token, loads the user's data, calls the Claude Messages API with three tools (`add_meeting`, `add_commitment`, `complete_commitment`), writes with the Admin SDK, and returns what it saved so the UI can show Undo cards.

## Not built yet
Billing and plans, the morning brief, charts, example data, rate limiting on `/api/chat`, and importing from Google Calendar or Gmail.
