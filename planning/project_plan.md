# Project Plan

Pod Members: **Brandon Curo, Prateek Oblumpally, Charles Mada**


## Problem Statement and Description

Problem: Google Maps labels venues as "wheelchair accessible" without showing critical details like door widths, ramp slopes, or turning space. This leaves millions of Americans who use wheelchairs - or different handicap vehicles on wheels - unable to confidently plan visits. Wheelchair users currently rely on incomplete information or must physically scout locations in advance, wasting time and even risking their safety.

Solution: Community photos into a trustworthy database where users can upload venue photos. An AI vision model automatically detects features like ramps and grab bars with labeled confidence scores. The community can then verifies each detection and adds information what photos can’t capture (door widths, ramp slopes, grab bars) producing a 0-100 accessibility score for each venue. 


## User Roles and Personas

Persona 1: David Martinez
Demographics:
Age: 28
Occupation: Software Engineer
Location: San Francisco, CA
Disability: Manual wheelchair user (spinal cord injury, T6-T7)
Tech Profile:
Very tech-savvy, early adopter
Uses smartphone for everything (iPhone, Google Maps, Yelp)
Active on social media and review platforms
Goals:
Discover new restaurants, cafes, and cultural venues weekly
Maintain independence without needing to scout locations in advance
Share accessibility information to help others
Pain Points:
"Wheelchair accessible" on Google Maps doesn't tell me if the door is 32" or 36"
Wasted 45 minutes last month traveling to a "accessible" museum that had narrow doorways
Has to call venues ahead or rely on outdated forum posts


Persona 2: Sarah Chen 
Demographics:
Age: 42
Occupation: High School Teacher
Location: Seattle, WA
Context: Mother of a 12-year-old son (David) who uses a power wheelchair
Tech Profile:
Moderate tech comfort, uses apps for navigation and planning
Prefers detailed information over surprises
Values community-verified data
Goals:
Plan family outings where David can participate fully
Avoid situations where David feels excluded or embarrassed
Find venues with accessible restrooms and parking nearby
Pain Points:
Power wheelchairs are wider (28"–30") and need more turning space than manual chairs
Reviews rarely mention door widths, ramp slopes, or restroom grab bar placement
Has driven to venues only to find they're not accessible for power chairs


## User Stories

User Story 1: Search by Accessibility Features
As a wheelchair user
I want to search venues filtered by ramp entrance
So that I can quickly find places I can safely visit without calling ahead
Acceptance Criteria:
User can select multiple accessibility filters (ramp, wide doors)
Search results update in real-time as filters are applied
Map shows only venues matching selected criteria
Results display distance from user's current location
Each result shows accessibility score (0-100)

User Story 2: View Detailed Venue Accessibility
As a wheelchair user


I want to view detailed accessibility information for a venue including door widths, ramp slopes, and community-verified photos


So that I can make an informed decision about whether I can visit safely
Acceptance Criteria:
Venue detail page shows accessibility score (0-100) with breakdown by feature
Each feature (ramp, door, restroom, parking) shows verification status and community notes
Community reviews include specific accessibility details (e.g., "34-inch door, smooth concrete ramp")
"Get Directions" button links to Google Maps
User can save venue to favorites


## Pages/Screens

1. Landing/Home - Home page with recommendations and search
2. Search/Map - Interactive map with venue filters
3. Venue Detail - Venue scores, photos, and reviews
4. Photo Upload - Real-time AI photo upload verification
5. User Dashboard - User stats, activity, and recommendations
6. Settings/Profile - Manage account and privacy settings
7. Add Venue - Add venues with feature checklist
8. About - Mission, AI explanations, and guidelines

Settings/Profile wireframe: https://www.figma.com/make/IoS1B3XUNz60eG1ujJiXbA/Create-user-dashboard-wireframe?t=3D00kBxsyJcINPTJ-6
User Dashboard Wireframe: https://www.figma.com/make/3bAwGe15PPs2bqEvzN8Zcl/user-dashboard-wireframe?t=3D00kBxsyJcINPTJ-6
Add Venue Wireframe: https://www.figma.com/make/kabYJC7aB8F1G2BqOODA3A/Add-Venue-Wireframe?t=80Htys3u6mHTpjs6-0


## Data Model

### 1. Users Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| email | String | Unique email address |
| passwordHash | String | Hashed password (nullable if OAuth only) |
| googleId | String | Google OAuth ID (unique, nullable) |
| displayName | String | Display name |
| disabilityProfile | JSON | User's accessibility needs (optional) |
| createdAt | DateTime | Account creation timestamp |

### 2. Venues Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| name | String | Venue name (e.g., "Palace of Fine Arts") |
| address | String | Street address |
| city | String | City name |
| state | String | State/province |
| zipCode | String | Postal code |
| latitude | Float | Latitude coordinate |
| longitude | Float | Longitude coordinate |
| placeId | String | Google Places ID (unique, nullable) |
| venueType | String | Category (e.g., "restaurant", "museum") |
| accessibilityScore | Integer | 0-100 calculated score (default: 0) |
| totalReviews | Integer | Count of reviews (default: 0) |
| totalPhotos | Integer | Count of photos (default: 0) |
| mlVerifiedFeatures | Integer | Count of AI-verified features (default: 0) |
| createdAt | DateTime | When venue was added |
| updatedAt | DateTime | Last update timestamp |

**Indexes:**
- `latitude, longitude` (for geospatial search)
- `city, state` (for location filtering)

**Relationships:**
- One venue → Many photos
- One venue → Many reviews
- One venue → Many venue features
- One venue → Many saved by users

### 3. Photos Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| venueId | UUID | Foreign key → venues.id |
| userId | UUID | Foreign key → users.id (uploader) |
| imageUrl | String | Cloudinary URL (full size) |
| thumbnailUrl | String | Cloudinary URL (thumbnail) |
| mlAnalyzed | Boolean | Whether AI has analyzed (default: false) |
| mlAnalysisId | UUID | → mlAnalysis.id (nullable) |
| uploadedAt | DateTime | Upload timestamp |

**Relationships:**
- Many photos → One venue
- Many photos → One user (uploader)
- One photo → One ML analysis (optional)
- One photo → Many detections

### 4. MLAnalysis Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| photoId | UUID | Foreign key → photos.id (unique) |
| modelVersion | String | AI model used (e.g., "yolov8s-world") |
| processingTime | Float | Seconds to analyze |
| totalDetections | Integer | Number of features detected |
| highConfidence | Integer | Detections above 85% confidence |
| analyzedAt | DateTime | Analysis timestamp |

**Relationships:**
- One ML analysis → One photo
- One ML analysis → Many detections

### 5. Detections Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| photoId | UUID | Foreign key → photos.id |
| mlAnalysisId | UUID | Foreign key → mlAnalysis.id |
| cocoLabel | String | Matched detection prompt (e.g., "door", "stairs") |
| accessibilityFeature | String | Mapped feature (e.g., "entrance_detected") |
| confidence | Float | 0.0-1.0 confidence score |
| boundingBox | JSON | `{x, y, width, height}` coordinates |
| verified | Boolean | Community verified (default: false) |
| verificationCount | Integer | Number of verifications (default: 0) |

**Index:**
- `accessibilityFeature` (for feature filtering)

**Relationships:**
- Many detections → One photo
- Many detections → One ML analysis

### 6. VenueFeatures Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| venueId | UUID | Foreign key → venues.id |
| featureType | String | Feature name (e.g., "rampEntrance", "wideDoors") |
| mlDetected | Boolean | Whether AI detected this (default: false) |
| mlConfidence | Float | AI confidence (nullable) |
| communityVerified | Boolean | Community confirmed (default: false) |
| verifiedCount | Integer | Number of verifications (default: 0) |
| notes | Text | Community notes (nullable) |
| lastVerifiedAt | DateTime | Last verification timestamp (nullable) |
| createdAt | DateTime | Feature added timestamp |

**Unique Constraint:** `venueId + featureType` (can't have duplicate features per venue)

**Relationships:**
- Many venue features → One venue

### 7. Reviews Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| venueId | UUID | Foreign key → venues.id |
| userId | UUID | Foreign key → users.id |
| rating | Integer | 1-5 stars |
| comment | Text | Review text |
| visitDate | DateTime | When user visited (nullable) |
| helpfulCount | Integer | Number of "helpful" votes (default: 0) |
| createdAt | DateTime | Review timestamp |

### 8. Verifications Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| detectionId | UUID | Foreign key → detections.id |
| userId | UUID | Foreign key → users.id |
| isAccurate | Boolean | User confirms detection is correct |
| notes | Text | Optional notes (nullable) |
| verifiedAt | DateTime | Verification timestamp |

**Unique Constraint:** `detectionId + userId` (user can only verify each detection once)

**Relationships:**
- Many verifications → One detection
- Many verifications → One user

### 9. SavedVenues Table (Join Table)

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| userId | UUID | Foreign key → users.id |
| venueId | UUID | Foreign key → venues.id |
| savedAt | DateTime | Bookmark timestamp |

**Unique Constraint:** `userId + venueId` (can't save same venue twice)

**Relationships:**
- Many saved venues → One user
- Many saved venues → One venue

### 10. Activity Table

| Column | Type | Description |
|---|---|---|
| id | UUID | Primary key |
| userId | UUID | Foreign key → users.id |
| type | String | Activity type (e.g., "photo_uploaded", "review_posted") |
| description | String | Human-readable description |
| metadata | JSON | Extra data (nullable) |
| createdAt | DateTime | Activity timestamp |

**Index:**
- `userId, createdAt` (for user activity feed)

**Relationships:**
- Many activities → One user

## Endpoints

These are the API contracts between the AccessMap frontend and backend. All request/response bodies are JSON unless noted (photo upload uses `multipart/form-data`). Authenticated routes expect a `Bearer` token in the `Authorization` header; `401 Unauthenticated` applies to every authenticated route and is only re-listed when it is the primary failure case. Timestamps are ISO 8601 strings.

### Auth & Session

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Create | POST | `/api/auth/register` | Register a new account | `{ email, password, displayName, role }` | `{ token, user: { id, email, displayName, username, role, createdAt } }` | 409 if email already registered, 422 if password/email invalid | 5, 7 |
| Create | POST | `/api/auth/login` | Log in with credentials | `{ email, password }` | `{ token, user: { id, email, displayName, username, role } }` | 401 if credentials invalid, 422 if fields missing | 1, 10 |
| Create | POST | `/api/auth/oauth/:provider` | Log in / link via OAuth (google, apple) | `{ code, redirectUri }` | `{ token, user: {...}, linked: true }` | 400 if provider unsupported, 401 if code invalid | — |
| Create | POST | `/api/auth/logout` | Invalidate the current session token | — | `{ success: true }` | 401 if unauthenticated | — |
| Read | GET | `/api/auth/me` | Get the currently authenticated user | — | `{ id, email, displayName, username, role, level, badge, createdAt }` | 401 if unauthenticated | 7 |

### Users & Profile

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Read | GET | `/api/users/:userId` | Fetch a user's public profile + stats | — | `{ id, displayName, username, location, bio, role, level, badge, statsPublic, stats: { photosUploaded, featuresDetected, peopleHelped, venuesVerified } }` | 404 if user not found | 7 |
| Update | PATCH | `/api/users/:userId` | Update profile fields (owner only) | `{ displayName?, username?, location?, bio?, role?, statsPublic? }` | `{ id, displayName, username, location, bio, role, statsPublic }` | 403 if not owner, 409 if username taken, 422 if invalid | 7 |
| Update | PUT | `/api/users/:userId/avatar` | Upload/replace profile photo | `multipart/form-data` (`image`) | `{ avatarUrl }` | 403 if not owner, 413 if file too large, 415 if bad type | — |
| Read | GET | `/api/users/:userId/accessibility-needs` | Get saved accessibility filters/preferences | — | `{ features: [string], defaultRadiusMi, minScore }` | 403 if not owner, 404 if user not found | 1, 8 |
| Update | PUT | `/api/users/:userId/accessibility-needs` | Save accessibility needs that seed search & recs | `{ features: [string], defaultRadiusMi, minScore }` | `{ features, defaultRadiusMi, minScore }` | 403 if not owner, 422 if invalid feature key/score | 1, 8 |
| Read | GET | `/api/users/:userId/preferences` | Get display & accessibility UI preferences | — | `{ theme, textSize, highContrast, reduceMotion, screenReaderLabels, alwaysShowBoundingBoxes }` | 403 if not owner | — |
| Update | PUT | `/api/users/:userId/preferences` | Update display/UI preferences | `{ theme?, textSize?, highContrast?, reduceMotion?, screenReaderLabels?, alwaysShowBoundingBoxes? }` | `{ ...updated preferences }` | 403 if not owner, 422 if invalid value | — |
| Update | PUT | `/api/users/:userId/notifications` | Update notification opt-ins | `{ photoVerified?, reviewOnSaved?, newNearbyVenues?, weeklySummary? }` | `{ ...notification settings }` | 403 if not owner | 7, 9, 10 |
| Update | POST | `/api/users/:userId/password` | Change password | `{ currentPassword, newPassword }` | `{ success: true }` | 401 if current wrong, 403 if not owner, 422 if weak | — |
| Delete | DELETE | `/api/users/:userId` | Delete account (danger zone) | `{ confirm: true }` | `{ success: true }` | 403 if not owner, 400 if not confirmed | — |

### Venues

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Create | POST | `/api/venues` | Create a new venue (Step 1 of Add Venue) | `{ name, address, category, lat, lng }` | `{ id, name, address, category, lat, lng, score, createdBy, createdAt }` | 401 if unauthenticated, 409 if duplicate venue nearby, 422 if invalid | 5 |
| Read | GET | `/api/venues/:venueId` | Get full venue detail (score, features, photos, reviews) | — | `{ id, name, address, category, lat, lng, score, features: [{ key, label, verified, confidence, communityNotes }], photos: [{ id, url, detections }], reviewSummary: { avgRating, count } }` | 404 if venue not found | 2, 3, 4 |
| Read | GET | `/api/venues` | List/browse venues (paginated, optional bbox) | Query: `?bbox=&category=&page=&limit=` | `{ venues: [{ id, name, category, lat, lng, score, thumbnailUrl }], page, total }` | 422 if bad bbox/params | 8 |
| Update | PATCH | `/api/venues/:venueId` | Edit venue metadata (name, address, category) | `{ name?, address?, category?, lat?, lng? }` | `{ id, name, address, category, lat, lng }` | 401 if unauthenticated, 404 if not found, 422 if invalid | 5 |
| Read | GET | `/api/venues/:venueId/features` | Get the accessibility-feature breakdown + score math | — | `{ score, features: [{ key, label, verified, confidence, verificationCount, notes }] }` | 404 if venue not found | 2, 3, 9 |

### Search & Recommendations (AI)

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Read | POST | `/api/search` | Natural-language venue search (Claude interprets query) | `{ query, lat?, lng?, radiusMi?, filters?: [string], minScore? }` | `{ interpretedFilters: [string], results: [{ id, name, score, distanceMi, matchedFeatures: [string], explanation, thumbnailUrl }] }` | 422 if query empty, 503 if AI service unavailable | 1, 8 |
| Read | GET | `/api/search/filters` | Get the canonical list of filterable accessibility features | — | `{ groups: [{ label, features: [{ key, label, icon }] }] }` | — | 8 |
| Read | GET | `/api/recommendations` | AI recommendations tailored to the user's needs & location | Query: `?lat=&lng=&limit=` | `{ recommendations: [{ id, name, score, distanceMi, matchedFeatures, explanation, thumbnailUrl, verified }] }` | 401 if unauthenticated | 1, 3, 8 |

### Photos & AI Detection

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Create | POST | `/api/venues/:venueId/photos` | Upload one or more photos to a venue (Step 2) | `multipart/form-data` (`images[]`, `caption?`) | `{ photos: [{ id, url, uploadedBy, uploadedAt, status: "uploaded" }] }` | 401 if unauthenticated, 404 if venue not found, 413 if too large, 415 if bad type | 2, 5 |
| Create | POST | `/api/photos/:photoId/analyze` | Run the CV model (YOLO-World) to detect accessibility features (Step 3) | — | `{ photoId, detections: [{ featureKey, label, confidence, boundingBox: { x, y, w, h } }], altTextSuggestion }` | 404 if photo not found, 422 if unreadable image, 503 if ML service down | 6, AI |
| Read | GET | `/api/photos/:photoId` | Get a photo with its confirmed detections + bounding boxes | — | `{ id, url, venueId, uploadedBy, detections: [{ featureKey, label, confidence, boundingBox, confirmed }], altText }` | 404 if photo not found | 2, AI |
| Update | PATCH | `/api/photos/:photoId/detections` | Confirm/reject AI detections, add manual features, edit alt text (Step 3 → 4) | `{ confirmed: [featureKey], rejected: [featureKey], manual?: [{ featureKey }], altText? }` | `{ photoId, detections: [{ featureKey, confirmed }], altText }` | 403 if not uploader, 404 if photo not found, 422 if invalid featureKey | 6, AI |
| Delete | DELETE | `/api/photos/:photoId` | Remove a photo (uploader or admin) | — | `{ success: true }` | 403 if not owner/admin, 404 if photo not found | 5 |

### Contributions (Add Venue submit)

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Create | POST | `/api/contributions` | Submit a completed contribution (Step 4): venue + confirmed photos/features enter verification queue | `{ venueId, photoIds: [id], confirmedFeatures: [featureKey], note? }` | `{ id, venueId, previewScore, status: "pending_verification", featuresConfirmed, createdAt }` | 401 if unauthenticated, 404 if venue/photo not found, 422 if no photos/features | 3, 5, 6, 9 |
| Read | GET | `/api/venues/:venueId/photos/preview-score` | Preview the auto-calculated score before submitting | Query: `?featureKeys=` | `{ previewScore, contributingFeatures: [featureKey] }` | 404 if venue not found, 422 if bad feature keys | 3 |

### Community Verification

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Read | GET | `/api/verification/queue` | Get photos/features awaiting community verification | Query: `?venueId=&page=&limit=` | `{ items: [{ photoId, venueId, url, detections, uploadedBy }], page, total }` | 401 if unauthenticated | 9 |
| Create | POST | `/api/photos/:photoId/verifications` | Community-verify (confirm/dispute) a photo's detected features | `{ vote: "confirm" \| "dispute", featureKey?, note? }` | `{ photoId, featureKey, verificationCount, verified: boolean }` | 401 if unauthenticated, 404 if photo not found, 409 if user already voted, 422 if invalid vote | 9 |
| Create | POST | `/api/photos/:photoId/flag` | Flag a photo as fake/spam/inappropriate | `{ reason, note? }` | `{ success: true, flagId }` | 401 if unauthenticated, 404 if photo not found, 422 if reason missing | 9 |

### Reviews

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Create | POST | `/api/venues/:venueId/reviews` | Write a review with accessibility details | `{ rating, body, accessibilityDetails?: [string] }` | `{ id, venueId, userId, rating, body, accessibilityDetails, createdAt }` | 401 if unauthenticated, 404 if venue not found, 409 if already reviewed, 422 if rating out of range | 4 |
| Read | GET | `/api/venues/:venueId/reviews` | List reviews for a venue (paginated) | Query: `?page=&limit=&sort=` | `{ reviews: [{ id, user: { displayName, badge }, rating, body, accessibilityDetails, createdAt }], avgRating, page, total }` | 404 if venue not found | 4 |
| Update | PATCH | `/api/reviews/:reviewId` | Edit own review | `{ rating?, body?, accessibilityDetails? }` | `{ id, rating, body, accessibilityDetails, updatedAt }` | 403 if not author, 404 if review not found, 422 if invalid | 4 |
| Delete | DELETE | `/api/reviews/:reviewId` | Delete own review | — | `{ success: true }` | 403 if not author/admin, 404 if review not found | 4 |

### Favorites / Saved Venues

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Create | POST | `/api/favorites` | Save a venue to the user's favorites | `{ venueId }` | `{ id, userId, venueId, savedAt }` | 401 if unauthenticated, 404 if venue not found, 409 if already saved | 10 |
| Read | GET | `/api/favorites` | List the current user's saved venues | Query: `?page=&limit=` | `{ favorites: [{ id, venue: { id, name, score, thumbnailUrl }, savedAt }], page, total }` | 401 if unauthenticated | 10 |
| Delete | DELETE | `/api/favorites/:venueId` | Remove a venue from favorites | — | `{ success: true }` | 401 if unauthenticated, 404 if not in favorites | 10 |

### Dashboard

| CRUD | HTTP Verb | Endpoint | Description | Request Shape | Response Shape | Error Cases | User Stories |
|---|---|---|---|---|---|---|---|
| Read | GET | `/api/dashboard/stats` | Get the current user's stat tiles + level progress | — | `{ role, level, badge, stats: { photosUploaded, featuresDetected, peopleHelped, venuesVerified }, nextLevel: { target, current } }` | 401 if unauthenticated | 7 |
| Read | GET | `/api/dashboard/activity` | Reverse-chronological feed of the user's actions | Query: `?page=&limit=` | `{ activity: [{ type, venue?: { id, name }, detail, timestamp }], page, total }` | 401 if unauthenticated | 7, 9, 10 |


## State Architecture

How AccessMap manages state across the React frontend, the API layer, and the backend.

### Layers of State

| Layer | What it holds | Tooling | Lifetime |
|---|---|---|---|
| **Server state** | Venues, photos, detections, reviews, scores — anything owned by the backend | TanStack Query (React Query) | Cached, background-refetched, invalidated on mutation |
| **Global client state** | Auth session/token, current user, theme & accessibility UI preferences | React Context + `useReducer` | Session (persisted to `localStorage`) |
| **URL state** | Search query, active filters, map bounds, pagination | React Router search params | Shareable/bookmarkable |
| **Local component state** | Form inputs, stepper step, modal open/close, upload progress | `useState` / `useReducer` | Component mount |

### Key Decisions

- **Server state is never duplicated into global state.** React Query is the single source of truth for anything the API owns. Components read venues, reviews, and detections through query hooks (`useVenue`, `useReviews`) so cache invalidation after a mutation (e.g. submitting a contribution) automatically refreshes every view showing that data.
- **Auth lives in a top-level `AuthContext`.** Holds the JWT, decoded user, and `login`/`logout` actions. The token is persisted to `localStorage` and attached to every request by an Axios interceptor; a 401 response clears the context and redirects to login.
- **Accessibility preferences are global and persisted.** Theme, text size, high-contrast, reduce-motion, screen-reader labels, and "always show bounding boxes" live in a `PreferencesContext`, hydrated from the user's saved `preferences` on login and mirrored to `localStorage` for instant, flash-free application on load. These preferences also seed default search filters.
- **Search & filter state lives in the URL**, not component state — so a filtered search ("ramp + accessible restroom near me") is a shareable link and survives refresh/back-button.
- **The Add Venue stepper uses a local reducer.** The 4-step flow (Venue → Photos → AI Review → Submit) is a self-contained `useReducer` machine; only the final submit writes to the server, keeping the multi-step draft entirely client-side until confirmed.

### Data Flow Example — Submitting a Contribution

1. Contributor completes the Add Venue stepper (local reducer state).
2. Photos upload via `POST /api/venues/:id/photos` → React Query mutation.
3. `POST /api/photos/:id/analyze` returns detections; the AI-review step edits them in local state.
4. `POST /api/contributions` commits the contribution.
5. On success, React Query invalidates `venue`, `dashboard/stats`, and `dashboard/activity` caches → the venue detail page, the user's stats tiles, and the activity feed all refresh automatically.

## AI Feature Specification

### Primary Feature — Computer Vision Accessibility Detection

**User story:** *"As a contributor, I want AI to automatically detect accessibility features in my photos, so that I don't have to manually tag them."* (Stories #5, #6)

| Aspect | Specification |
|---|---|
| **Model** | PyTorch YOLO-World (open-vocabulary object detection), using pretrained `yolov8s-world` weights |
| **Input** | A venue photo (JPEG/PNG), uploaded to Cloudinary; the URL is passed to the ML service |
| **Output** | List of detections: `{ cocoLabel, accessibilityFeature, confidence (0.0–1.0), boundingBox {x,y,w,h} }` |
| **Mapping** | YOLO-World is prompted with plain-English phrases (e.g. "wheelchair ramp", "door", "stairs") and its matched prompt is mapped to an accessibility feature (detected ramp/door → `entrance_detected`, chair → `seating_available`, detected stairs → flag as a *barrier*) |
| **Confidence threshold** | Detections ≥ 0.85 are surfaced as high-confidence and pre-checked; lower-confidence ones are shown but flagged for contributor review. Note: open-vocabulary detection often scores lower than fixed-class models, so a low confidence floor (~0.10) is used to surface borderline features rather than hide them |
| **Latency target** | < 3 seconds per photo (CPU inference on Render free tier; see Open Questions) |
| **Human-in-the-loop** | AI proposes, contributor confirms/rejects each detection (Step 3), and the community verifies afterward — AI is never the sole authority |

**Flow:**
1. Contributor uploads photo → stored in Cloudinary, `Photo` row created (`mlAnalyzed = false`).
2. `POST /api/photos/:id/analyze` calls the Python ML service.
3. Service runs YOLO-World, maps matched prompts to accessibility features, writes an `MLAnalysis` row + `Detection` rows, sets `mlAnalyzed = true`.
4. Frontend renders bounding boxes over the photo with confidence labels; results are also announced as text for screen-reader users.
5. Contributor confirms → detections feed `VenueFeatures` and the venue's `accessibilityScore`.

### Secondary Feature — Natural-Language Search & Recommendations

**User story:** *"As a venue visitor, I want to search using natural language, so that I can find places meeting my specific needs without complex filters."* (Stories #1, #8)

| Aspect | Specification |
|---|---|
| **Model** | Claude (Anthropic API) |
| **Input** | Free-text query + optional user location and saved accessibility needs |
| **Task** | Interpret the query into structured filters (feature keys, radius, min score), then rank matching venues and generate a plain-English explanation per result |
| **Output** | `{ interpretedFilters, results: [{ venue, matchedFeatures, explanation }] }` |
| **Grounding** | Claude only ranks/explains venues returned from the database — it does not invent venues or accessibility facts, avoiding hallucinated accessibility claims |
| **Fallback** | If the AI service is unavailable (503), the UI falls back to manual filter-based search using the same feature keys |

### Trust & Safety

- Every AI detection carries a visible confidence score and enters the **community verification queue** (`Verifications` table) — a detection is only "verified" after community confirmation.
- Contributors can uncheck false positives; visitors can flag fake/spam photos.
- The accessibility score weights community-verified features above AI-only detections.

## Decisions Log (Sprints 1–4)

A running log of key technical and product decisions across the build. Newest at the bottom of each sprint.

### Sprint 1 — Foundations & Planning

| Decision | Rationale | Alternatives Considered |
|---|---|---|
| Use PostgreSQL as the primary database | Relational data (venues → photos → detections) with strong FK constraints; PostGIS-ready for geospatial queries | MongoDB (rejected — relationships are core to the model) |
| Cloudinary for image storage | Free tier, automatic thumbnail generation, CDN delivery; keeps binaries out of the DB | Storing images as blobs (rejected — bloats DB, no CDN) |
| JWT bearer tokens for auth (+ Google OAuth) | Stateless, simple to attach via interceptor; OAuth lowers signup friction | Server sessions (rejected — adds session store infra) |
| Split ML into a separate Python service | PyTorch/YOLO-World needs Python; keeps the Node/Express API lightweight | Running inference in-process (rejected — language mismatch) |

### Sprint 2 — Core CRUD & Data Layer

| Decision | Rationale | Alternatives Considered |
|---|---|---|
| TanStack Query for all server state | Eliminates manual loading/error/cache code; automatic invalidation on mutation | Redux Toolkit (rejected — heavier, more boilerplate for our needs) |
| Store search/filter state in the URL | Makes filtered searches shareable and refresh-safe | Global store (rejected — not shareable, loses back-button) |
| Separate `MLAnalysis` and `Detection` tables | One analysis run yields many detections; keeps model metadata (version, timing) distinct from per-feature results | Single denormalized table (rejected — duplicated run metadata) |
| Denormalize counts onto `Venues` (`totalReviews`, `accessibilityScore`) | Avoids expensive aggregate queries on every venue-list render | Compute on read (rejected — slow list/map views) |

### Sprint 3 — AI Integration

| Decision | Rationale | Alternatives Considered |
|---|---|---|
| Use pretrained `yolov8s-world` (YOLO-World), prompting it with accessibility phrases | Open-vocabulary detection can find ramps, doors, and stairs directly from text prompts — COCO's 80 fixed classes have no ramp/grab-bar labels, making the old label-mapping approach weak; ships an MVP without a custom-trained model | Start from `yolov5s` + map COCO labels (rejected — COCO lacks accessibility classes); train custom model first (deferred — data collection risk, see Open Questions) |
| 0.85 confidence threshold for "high confidence" | Balances precision vs. recall; low-confidence detections still shown but not pre-checked | Hard cutoff hiding <0.85 (rejected — loses useful borderline signals) |
| Human-in-the-loop confirmation required | AI can be wrong; contributor + community verification protects trust | Auto-publish AI detections (rejected — undermines trust, our core value) |
| Claude only ranks DB venues, never invents facts | Prevents hallucinated accessibility claims that could endanger users | Free-form generative answers (rejected — safety risk) |

### Sprint 4 — Polish, Trust & Deployment

| Decision | Rationale | Alternatives Considered |
|---|---|---|
| Community verification queue gates "verified" status | Turns crowd input into trust; a detection isn't authoritative until confirmed | AI-only verification (rejected — no human check) |
| Accessibility preferences persisted to `localStorage` + hydrated on login | Flash-free theme/contrast on load; consistent across devices via saved prefs | Server-only (rejected — flash of default theme on every load) |
| Deploy web + API on Render, ML service separately | Matches the service split; free tier viable for MVP scale | Single monolith deploy (rejected — Python/Node mismatch) |
| Graceful AI fallback to manual filters on 503 | Search must keep working even if the AI service is down | Hard failure (rejected — breaks core discovery flow) |

*This log is updated continuously as decisions are made and revisited throughout the sprints.*
