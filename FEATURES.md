# Feature Documentation

[← Back to README](../README.md)

This document details how each core feature of Basma 360° was implemented — the Cubits, repositories, and design decisions behind them.

---

## 1. Authentication

Authentication forms the foundation of user access and is managed by `register_cubit.dart` and `login_cubit.dart`, handling state transitions (loading, success, error).

**Registration flow:**
- Users select a role via enums in `role_check.dart` (Volunteer or Organization), routing to differentiated forms (`volunteer_form.dart` / `organization_form.dart`).
- Inputs are validated via `validators.dart` (email format, password strength, etc.).
- On submission, the Cubit calls `auth_repo.dart`, which uses `supabase.auth.signUp()`.
- User data is stored in role-specific Supabase tables (`volunteers` or `organizations`), with shared fields (name, email) common to both.
- A **Firebase**-sent verification email confirms the user's address before app access is granted.

**Login & session:**
- `supabase.auth.signInWithPassword()` establishes the session.
- After login, the app determines the user's role and dynamically renders the relevant fields and UI.

**Design notes:**
- Role enums enable dynamic UI routing — a single codebase serving two distinct user experiences without duplicating the app.
- Async operations are wrapped in try-catch with failure mapping (`failures.dart`) to prevent crashes and surface user-friendly error messages (e.g., "Network error — retry?").

---

## 2. Profile Management

Handles viewing and editing personal data — central to both matching and trust.

- After registration, every user's profile is pre-populated with basic data (name, email, role); users can upload an avatar.
- **Volunteers** can additionally:
  - Add a bio and skills
  - Take the **Skills Assessment Test**, with results inserted into `volunteer_skills` (linked to `skills_master` by ID)
  - View applied opportunities and their current status
  - View a **Volunteer History** of completed/closed opportunities
- **Organizations** can:
  - Upload verification documents (mandatory before posting opportunities)
  - View/manage contact info
  - Display a verification badge once approved

**Technical implementation:** separate Cubits (`volunteer_profile_cubit.dart`, `organization_cubit.dart`) fetch data through dedicated repositories, using Supabase queries with joins (e.g. to applications/history). Updates use targeted `eq('id', userId)` calls to avoid overwriting unrelated fields, with realtime subscriptions for live refresh. Profiles load in under 2 seconds in testing.

---

## 3. Opportunities

The core feature of the platform — allowing organizations to post volunteering needs and volunteers to discover and apply, enhanced by AI matching.

**Posting flow** (`opportunities_cubit.dart` / `OrganizationOpportunitiesCubit`):
- `OpportunityForm` widget captures: title & description, category/field, a searchable skills selector, target audience, dates (with logical validation — e.g., end date ≥ 30 days after start, deadline ≤ start), a remote toggle (disables location fields when active), an image picker (uploaded to Supabase Storage), a location section (resolved via the `geocode-location` edge function), and volunteer count/duration.
- Validated via `opportunity_validators.dart` (required fields, logical dates, ≥1 skill, verified location for non-remote postings).
- On submission, data is written to `opportunities`, with selected skills stored in the `opportunity_skills` junction table.

**Browsing & details:**
- Opportunities render as `OpportunityCard` widgets with image, title, organization info, category/status badges, skill tags, a match-score badge (for volunteers), and date/location info.
- `OpportunityDetailsScreen` shows a hero image, organization header, capacity statistics, description, dates/details, skills, and contact info.
- Role-based actions: organizations see Edit/Applicants; volunteers see an Apply button that toggles to "Unapply."

**Matching & recommendations:**
- Personalized filtering by location and skills.
- Calls the `match-opportunities` edge function → `match_opportunities_for_volunteer` RPC (see [`AI_MATCHING.md`](AI_MATCHING.md)).

**State & data behavior:**
- Organizations can view an **Applicants List** per opportunity, showing each applicant's match score to support acceptance decisions.
- Applying updates the `applications` table (volunteer → opportunity); re-clicking toggles to "Unapply."
- Volunteers must set a location after registration for accurate matching; remote opportunities bypass location validation.
- Once the accepted-volunteer count reaches capacity, the opportunity auto-closes and cards display a "Full" status.

---

## 4. Skills Assessment Test

Assesses volunteer competencies in a structured, data-driven way rather than relying solely on self-declared skills.

- `skills_test_cubit.dart` manages the full lifecycle: initializing state, loading questions, tracking answers, validating completeness, and handling submission states.
- On submission, answers are sent via the `submit_skill_test_answers` RPC, which validates input, persists responses, and calculates derived skill indicators.
- A key part of this process is generating a **vector embedding** from the submitted answers, representing the volunteer's skill profile numerically — enabling semantic comparison during matching. This embedding is stored and linked to the volunteer profile, so future matches reflect assessed capability rather than static profile text.
- This design allows matching accuracy to improve continuously as more assessment data becomes available.

---

## 5. Realtime Chat (Firebase Integration)

Implemented using **Firebase Firestore** for secure, realtime communication between volunteers and organizations, chosen for its realtime capabilities and fine-grained security rules.

**Chat room management:**
- Each conversation is a chat-room document with a `members` list.
- The system supports "get-or-create" behavior to avoid duplicate rooms.
- Access is strictly restricted at the database level:
  - Reading/listing a room requires membership.
  - Creating a room requires the authenticated user to be included in `members` (preventing creating rooms on someone else's behalf).
  - Updating requires membership.
  - **Deleting is explicitly disabled** to preserve conversation history.

**Message handling:**
- Messages are stored as subcollections under their chat room.
- Reading is restricted to room members; writing validates that the sender ID matches the authenticated user, preventing impersonation.

**Context-based entry points:**
- A volunteer can start a chat from a read-only organization profile view (reached through a listed opportunity).
- An organization can only start a chat with a volunteer **after** that volunteer has applied to one of its opportunities.
- This context-based model reduces unsolicited messaging and keeps conversations tied to actual volunteering activity.

**Security rationale:** combining Firebase Authentication, Firestore security rules, and context-aware entry points enforces critical permissions on the backend — meaning unauthorized access isn't possible even if client-side logic were bypassed. Realtime updates were measured at under 1 second in testing.

---

## 6. Organization Verification

Ensures trust and credibility on the platform.

- Organizations upload official verification documents (e.g. registration/authorization) via `verification_repo.dart`, which securely uploads files to backend storage.
- Upon registration, a home-screen prompt reminds organizations to submit verification documents.
- **Organizations cannot post opportunities until verified** — this gate prevents unverified entities from publishing and protects volunteers from interacting with unconfirmed organizations.
- Once verified, a badge (checkmark icon) appears on the organization's profile.

---

## 7. Location Management

Location input is **mandatory for all users**, requested immediately after registration, to enable location-aware recommendations and accurate matching.

- `location_cubit.dart` manages location selection, permission handling, and state updates.
- The `geocode-location` backend function converts user-provided location into precise latitude/longitude coordinates.
- Implemented using the `geolocator` (device location/permissions) and `geocoding` (coordinate resolution) Flutter packages.
- The Home screen displays the user's current location with a personalized welcome message and a manual refresh button to update location if the user has moved.

---

## 8. Settings

Provides controlled access to account management and app preferences, with sensitive operations routed through Firebase Authentication.

**General settings:**
- Language display, with navigation prepared for a future language-selection screen (multilingual support planned).
- Theme mode switching (light/dark), applied consistently across the app.

**Account management (Firebase-backed):**
- **Change Password** — validates the user's current authentication state via Firebase before applying a change.
- **Account Deletion** — requires re-entering the current password before Firebase re-authenticates and permanently deletes the account and its credentials, preventing accidental/malicious deletion.
- **Logout** — securely clears the active Firebase session and any locally cached credentials.

All sensitive operations are enforced at the backend/Firebase level, so client-side manipulation alone cannot bypass these security checks.
