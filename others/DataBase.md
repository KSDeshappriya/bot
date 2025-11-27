# ✅ **FULL SQL SCHEMA (PostgreSQL)**


## **1. ENUM DEFINITIONS**

```sql
CREATE TYPE user_role AS ENUM ('TRAVELER', 'GUIDE', 'ADMIN');
CREATE TYPE gender AS ENUM ('MALE', 'FEMALE', 'OTHER', 'PREFER_NOT_TO_SAY');
CREATE TYPE trip_preference AS ENUM ('RELIGIOUS', 'CASUAL', 'ADVENTURE');
CREATE TYPE booking_status AS ENUM ('PENDING', 'ACCEPTED', 'CONFIRMED', 'COMPLETED', 'CANCELLED');
CREATE TYPE payment_status AS ENUM ('PENDING', 'PAID', 'RELEASED', 'CANCELLED');
CREATE TYPE planning_mode AS ENUM ('MANUAL', 'AI_GENERATED');
CREATE TYPE trip_status AS ENUM ('PLANNING', 'CONFIRMED', 'IN_PROGRESS', 'COMPLETED', 'CANCELLED');
CREATE TYPE ad_status AS ENUM ('PENDING', 'ACTIVE', 'INACTIVE', 'REJECTED');
CREATE TYPE workflow_type AS ENUM ('GENERATE_AI_PLAN', 'CREATE_MANUAL_PLAN');
CREATE TYPE reviewer_type AS ENUM ('TRAVELER', 'GUIDE');
```

---

## **2. USERS TABLE**

```sql
CREATE TABLE users (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  email TEXT NOT NULL UNIQUE,
  password TEXT NOT NULL,
  role user_role NOT NULL,
  phone TEXT NOT NULL,
  name TEXT NOT NULL,
  birth_year INTEGER NOT NULL,
  gender gender NOT NULL,
  languages TEXT[] NOT NULL,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL,
  updated_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **3. TRAVELERS TABLE**

```sql
CREATE TABLE travelers (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id TEXT NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
  country TEXT NOT NULL,
  rating REAL DEFAULT 0 NOT NULL,
  total_reviews INTEGER DEFAULT 0 NOT NULL,
  trip_in_progress BOOLEAN DEFAULT FALSE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **4. GUIDES TABLE**

```sql
CREATE TABLE guides (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id TEXT NOT NULL UNIQUE REFERENCES users(id) ON DELETE CASCADE,
  nic TEXT NOT NULL UNIQUE,
  rating REAL DEFAULT 0 NOT NULL,
  total_reviews INTEGER DEFAULT 0 NOT NULL,
  trip_in_progress BOOLEAN DEFAULT FALSE NOT NULL,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **5. TRIPS TABLE**

```sql
CREATE TABLE trips (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  traveler_id TEXT NOT NULL REFERENCES travelers(id) ON DELETE CASCADE,
  from_date TIMESTAMP NOT NULL,
  to_date TIMESTAMP NOT NULL,
  number_of_people INTEGER NOT NULL,
  country TEXT NOT NULL,
  preferences trip_preference[],
  plan_description TEXT,
  planning_mode planning_mode NOT NULL,

  ai_summary TEXT,
  ai_recommendations TEXT[],
  feasibility_score INTEGER,

  total_distance REAL,
  estimated_cost REAL,

  daily_itinerary JSONB,

  guide_id TEXT REFERENCES guides(id) ON DELETE SET NULL,
  needs_guide BOOLEAN DEFAULT FALSE NOT NULL,

  status trip_status DEFAULT 'PLANNING' NOT NULL,
  booking_status booking_status DEFAULT 'PENDING' NOT NULL,

  created_at TIMESTAMP DEFAULT NOW() NOT NULL,
  updated_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **6. TRIP LOCATIONS TABLE**

```sql
CREATE TABLE trip_locations (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  trip_id TEXT NOT NULL REFERENCES trips(id) ON DELETE CASCADE,
  title TEXT NOT NULL,
  address TEXT NOT NULL,
  district TEXT NOT NULL,
  latitude REAL NOT NULL,
  longitude REAL NOT NULL,
  category TEXT NOT NULL,
  rating REAL,
  day_number INTEGER NOT NULL,
  visit_order INTEGER NOT NULL,
  estimated_duration TEXT,
  reason_for_selection TEXT,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **7. PLACES (ATTRACTIONS) TABLE**

```sql
CREATE TABLE places (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  name TEXT NOT NULL,
  location TEXT NOT NULL,
  images TEXT[] NOT NULL,
  description TEXT NOT NULL,
  significance TEXT NOT NULL,
  ticket_price REAL,
  category trip_preference[] NOT NULL,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **8. EVENTS TABLE**

```sql
CREATE TABLE events (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  title TEXT NOT NULL,
  images TEXT[] NOT NULL,
  date TEXT NOT NULL,
  price TEXT NOT NULL,
  place TEXT NOT NULL,
  lat REAL NOT NULL,
  lng REAL NOT NULL,
  phone TEXT NOT NULL,
  organizer TEXT NOT NULL,
  description TEXT NOT NULL,
  ticket_count INTEGER DEFAULT 0 NOT NULL,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL,
  updated_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **9. PAYMENTS TABLE**

```sql
CREATE TABLE payments (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  trip_id TEXT REFERENCES trips(id) ON DELETE CASCADE,
  event_id TEXT REFERENCES events(id) ON DELETE CASCADE,
  traveler_id TEXT REFERENCES travelers(id) ON DELETE CASCADE,
  ticket_quantity INTEGER,
  amount REAL NOT NULL,
  status payment_status DEFAULT 'PENDING' NOT NULL,
  stripe_session_id TEXT UNIQUE,
  paid_at TIMESTAMP,
  released_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **10. REVIEWS TABLE**

```sql
CREATE TABLE reviews (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  reviewer_id TEXT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  reviewee_id TEXT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  trip_id TEXT REFERENCES trips(id) ON DELETE SET NULL,
  rating INTEGER NOT NULL,
  comment TEXT,
  reviewer_type reviewer_type NOT NULL,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL,
  updated_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **11. GUIDE DECLINATIONS TABLE**

```sql
CREATE TABLE guide_declinations (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  guide_id TEXT NOT NULL REFERENCES guides(id) ON DELETE CASCADE,
  trip_id TEXT NOT NULL REFERENCES trips(id) ON DELETE CASCADE,
  declined_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **12. SERPER API USAGE LOGS**

```sql
CREATE TABLE api_usage_logs (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id TEXT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  query TEXT NOT NULL,
  category TEXT NOT NULL,
  location_name TEXT NOT NULL,
  result_count INTEGER NOT NULL,
  credits_used INTEGER NOT NULL,
  success BOOLEAN DEFAULT TRUE NOT NULL,
  error_message TEXT,
  response_time INTEGER,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **13. ADVERTISEMENTS TABLE**

```sql
CREATE TABLE advertisements (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  image_url TEXT NOT NULL,
  description TEXT NOT NULL,
  redirect_url TEXT NOT NULL,
  payment_reference TEXT NOT NULL UNIQUE,
  status ad_status DEFAULT 'PENDING' NOT NULL,
  view_count INTEGER DEFAULT 0 NOT NULL,
  submitted_at TIMESTAMP DEFAULT NOW() NOT NULL,
  updated_at TIMESTAMP DEFAULT NOW() NOT NULL,
  approved_at TIMESTAMP,
  approved_by TEXT REFERENCES users(id) ON DELETE SET NULL
);
```

---

## **14. AI USAGE LOGS TABLE**

```sql
CREATE TABLE ai_usage_logs (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id TEXT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
  workflow_type workflow_type NOT NULL,
  prompt_text TEXT NOT NULL,
  response_text TEXT,
  tokens_used INTEGER,
  success BOOLEAN NOT NULL,
  error_message TEXT,
  response_time INTEGER,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```

---

## **15. TRIP VERIFICATIONS TABLE**

```sql
CREATE TABLE trip_verifications (
  id TEXT PRIMARY KEY DEFAULT gen_random_uuid(),
  trip_id TEXT NOT NULL UNIQUE REFERENCES trips(id) ON DELETE CASCADE,
  otp TEXT NOT NULL,
  traveler_geohash TEXT NOT NULL,
  guide_geohash TEXT,
  verified BOOLEAN DEFAULT FALSE NOT NULL,
  verified_at TIMESTAMP,
  expires_at TIMESTAMP NOT NULL,
  created_at TIMESTAMP DEFAULT NOW() NOT NULL
);
```
