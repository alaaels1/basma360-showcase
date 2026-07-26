# Database Design

[← Back to README](../README.md)

---

The database is **PostgreSQL**, hosted on **Supabase**, designed with normalization in mind to minimize redundancy while supporting efficient queries for matching and realtime updates. Vector columns leverage the **`pgvector`** extension to enable semantic (embedding-based) search directly at the SQL level.

<div align="center">
<img src="../assets/supabase-schema.png" alt="Database Schema" width="85%"/>
</div>

---

## Core Tables

| Table | Purpose | Key Fields |
|---|---|---|
| `volunteers` | Stores volunteer user data | `id` (PK), `firebase_uid`, `name`, `email`, `bio`, `field`, `gender`, `birth_date`, `phone`, `avatar_url`, `field_bio_embedding`, `bio_embedding`, `latitude`, `longitude` |
| `organizations` | Stores organization profiles | `id` (PK), `firebase_uid`, `organization_name`, `email`, `organization_field`, `license_number`, `description`, `verification_document`, `is_verified`, `verification_status`, `latitude`, `longitude` |
| `opportunities` | Core table for volunteering postings | `id` (PK), `organization_id` (FK), `title`, `description`, `description_embedding`, `category`, `field_required`, `target_audience`, `is_remote`, `location_latitude`, `location_longitude`, `start_date`, `end_date`, `application_deadline`, `duration`, `total_volunteers_needed`, `status`, `image_url` |
| `applications` | Junction table linking volunteers to opportunities | `id` (PK), `volunteer_id` (FK), `opportunity_id` (FK), `organization_id` (FK), `status` |
| `skills_master` | Master catalog of all skills | `id` (PK), `skill_name`, `parent_skill`, `is_active` |
| `opportunity_skills` | Many-to-many junction: opportunities ↔ skills | `opportunity_id` (FK), `skill_id` (FK) |
| `volunteer_skills` | Skills associated with a volunteer (from profile or skills test) | `id` (PK), `volunteer_id` (FK), `skill_id` (FK) |
| `questions` | Skills Assessment Test question bank | `id` (PK), `question`, `options` (JSONB), `category` |
| `question_option_skills` | Maps a test answer option to the skill(s) it indicates | `id` (PK), `question_id` (FK), `option_key`, `skill_id` (FK) |
| `user_answers` | Stores a volunteer's Skills Test responses | `id` (PK), `volunteer_id` (FK), `question_id` (FK), `selected_option` |

---

## Relationships

- **One-to-many** — `organizations` → `opportunities` (via `organization_id`)
- **Many-to-many** — `opportunities` ↔ `volunteers` (via `applications`)
- **Many-to-many** — `opportunities` ↔ `skills_master` (via `opportunity_skills`)
- **Many-to-many** — `questions` ↔ `skills_master` (via `question_option_skills`)

---

## Vector Columns & Semantic Search

The following columns store vector embeddings, enabling similarity search via `pgvector`:

- `volunteers.field_bio_embedding` — embedding of the volunteer's professional field + bio (professional orientation)
- `volunteers.bio_embedding` — embedding of the bio alone (personal motivation/interests)
- `opportunities.description_embedding` — embedding of the opportunity description

These are compared using **cosine similarity/distance** at query time to compute the AI portion of the match score (see [`AI_MATCHING.md`](AI_MATCHING.md)).

**Trade-off:** normalization improves data integrity but increases the number of joins required for a full matching query. This is mitigated with indexes on foreign keys and by consolidating the matching logic into dedicated RPC functions rather than performing it client-side.

---

## Supporting SQL Functions (RPCs)

| Function | Purpose |
|---|---|
| `get_volunteer_match_score(volunteer_id, opportunity_id)` | Returns the detailed breakdown of matching scores (skills, location, gender, AI field+bio, AI bio, total) for a single volunteer–opportunity pair. Used mainly for validation, debugging, and explaining how each factor contributes to the final score. |
| `match_opportunities_for_volunteer(volunteer_id)` | Evaluates all active opportunities for a given volunteer using the same scoring logic, and returns a ranked list sorted by total match score. This is the function called by the `match-opportunities` Edge Function in production. |

Example output of `get_volunteer_match_score`:

| skills_match | location_match | gender_match | ai_field_bio | ai_bio_score | total_match_score |
|---|---|---|---|---|---|
| 100.00 | 0.00 | 100.00 | 1.31 | 0.82 | 45.45 |

Example output of `match_opportunities_for_volunteer` (ranked list for one volunteer):

| target_audience | skills_match | location_match | gender_match | ai_field_bio | ai_bio_score | total_match_score |
|---|---|---|---|---|---|---|
| Both | 100.00 | 100.00 | 100.00 | 34.67 | 35.13 | 73.94 |
| Female | 100.00 | 50.00 | 100.00 | 22.71 | 19.16 | 61.05 |
| Both | 100.00 | 0.00 | 100.00 | 1.31 | 0.82 | 45.45 |
| Both | 0.00 | 100.00 | 100.00 | 37.21 | 28.24 | 33.54 |

