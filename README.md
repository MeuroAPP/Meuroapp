# Meuro AI — working prototype

AI nutrition app for African users: onboarding, budget-aware meal planner, AI food scanner, food diary, AI assistant, and a verified doctor/dietitian network.

Live prototype (private until shared): https://claude.ai/artifact/DYPFeYAM2XdJrLxoEQw6yu

## Modules
| Module | What it does |
|---|---|
| Onboarding | 8 steps with consent: personal info, body, health conditions, medication, doctor's diet, goals, food preferences, budget (day/week/month), lifestyle. Generates an editable nutrition profile (Mifflin–St Jeor energy, protein, WHO sodium/sugar guides) and flags complex conditions for professional review. |
| Meal planner | AI composes meals from a priced ingredient table (Lagos market prices, Oct 2026). The app recomputes every meal's cost and nutrients itself, flags over-budget plans and offers "fit to budget". Replace meal, 1- or 7-day plans, adjust budget, favourites, shopping list. |
| Food scanner | Photo upload / camera → vision AI identifies foods, grams and confidence. Nutrients come from the Meuro African Food Table. User corrects, adds, removes items, then saves to diary. |
| Diary & tracker | Entries by meal slot; add, edit, delete; daily rings, 7/30-day trends, sodium and fibre alerts. |
| Assistant | Chat grounded in the user's profile, last 7 days of diary and current plan. No diagnosis; emergency symptoms → seek care. Optional saved history. |
| Experts | Professionals apply (name, email, phone, qualifications, licence no., specialisation, experience, profile, documents link). Admin approves/rejects; only approved professionals are listed. Users request consultations and see status. |

## Architecture (prototype)
- Single-file app: `index.html` (vanilla JS, inline SVG charts) + `img/`.
- Runs as a Claude artifact and uses its runtime capabilities:
  - `sample` (Claude, with images) for planning, scanning and chat.
  - `db` for storage. Private per user: `data/users/<id>/profile`, `plan`, `chat`, `diary_*` (`kind:"diary"`). Shared: `pros` (admin write), `applications/<uid>`, `consults/<uid>` (each user writes their own; admin reads all).
  - `user` for identity and the owner-only Admin tab.
- Opened directly from GitHub/a normal web host, the page shows a sign-in notice, because those capabilities only exist inside Claude.

## Production path
Move storage to the existing Supabase project (Postgres + row level security, same table shape as above), auth to Supabase Auth on meuroapp.com, and AI calls to a server-side function (e.g. Supabase Edge Function or Vercel function) holding the AI API key. Environment variables then needed: `SUPABASE_URL`, `SUPABASE_ANON_KEY` (public), and server-only `AI_API_KEY`. Never ship service-role or AI keys to the browser.

## Data sources
Meuro African Food Table v0.1: approximate values from the FAO/INFOODS West African Food Composition Table (2019) and USDA FoodData Central. Prices: Lagos State price tracker and Lagos market surveys (Oct 2026); items marked "est" are Meuro estimates.

## Limitations
- Nutrient values and portions are estimates, not lab measurements.
- Consultation booking is a request workflow; no payment or video call yet.
- Licence verification is manual by the admin.
- Not a medical device; gives nutrition guidance only.

## Demo
1. Complete onboarding (e.g. high blood pressure, ₦20,000/week).
2. Meal plan → Plan today (30–90 s).
3. Scan → upload a plate photo → correct a portion → save.
4. Dashboard: rings and weekly chart update.
5. Ask → tap a suggested question.
6. Experts → submit an application → Admin → approve → it appears in the list.
