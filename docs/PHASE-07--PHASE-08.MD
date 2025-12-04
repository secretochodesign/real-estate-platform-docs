# **07-DATABASE-SCHEMA.md**
````markdown
# DATABASE SCHEMA
## PostgreSQL Database Design

---

## CORE ENTITIES

### Users Table
```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  phone VARCHAR(20) UNIQUE,
  password_hash VARCHAR(255),
  
  -- Profile
  name VARCHAR(255) NOT NULL,
  avatar_url VARCHAR(500),
  bio TEXT,
  
  -- Type & Status
  user_type VARCHAR(20) NOT NULL CHECK (user_type IN ('buyer', 'seller', 'agent', 'developer')),
  status VARCHAR(20) DEFAULT 'active' CHECK (status IN ('status VARCHAR(20) DEFAULT 'active' CHECK (status IN ('active', 'inactive', 'banned', 'pending')),
  
  -- Verification
  email_verified BOOLEAN DEFAULT FALSE,
  phone_verified BOOLEAN DEFAULT FALSE,
  identity_verified BOOLEAN DEFAULT FALSE,
  
  -- OAuth
  google_id VARCHAR(255),
  facebook_id VARCHAR(255),
  
  -- Settings (JSONB for flexibility)
  settings JSONB DEFAULT '{}',
  
  -- Timestamps
  last_login_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_phone ON users(phone);
CREATE INDEX idx_users_type ON users(user_type);
CREATE INDEX idx_users_status ON users(status);
Cities Table
sqlCREATE TABLE cities (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name_en VARCHAR(100) NOT NULL,
  name_ka VARCHAR(100) NOT NULL,
  name_ru VARCHAR(100),
  slug VARCHAR(100) UNIQUE NOT NULL,
  country VARCHAR(50) DEFAULT 'Georgia',
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  is_active BOOLEAN DEFAULT TRUE,
  sort_order INT DEFAULT 0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
Districts Table
sqlCREATE TABLE districts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  city_id UUID REFERENCES cities(id) ON DELETE CASCADE,
  name_en VARCHAR(100) NOT NULL,
  name_ka VARCHAR(100) NOT NULL,
  name_ru VARCHAR(100),
  slug VARCHAR(100) NOT NULL,
  parent_district_id UUID REFERENCES districts(id), -- For sub-districts
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  polygon GEOMETRY(POLYGON, 4326), -- For map boundaries
  is_active BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  UNIQUE(city_id, slug)
);

CREATE INDEX idx_districts_city ON districts(city_id);
CREATE INDEX idx_districts_polygon ON districts USING GIST(polygon);
Metro Stations Table
sqlCREATE TABLE metro_stations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  city_id UUID REFERENCES cities(id) ON DELETE CASCADE,
  name_en VARCHAR(100) NOT NULL,
  name_ka VARCHAR(100) NOT NULL,
  name_ru VARCHAR(100),
  slug VARCHAR(100) NOT NULL,
  line_name VARCHAR(50),
  line_color VARCHAR(7), -- Hex color
  latitude DECIMAL(10, 8) NOT NULL,
  longitude DECIMAL(11, 8) NOT NULL,
  is_active BOOLEAN DEFAULT TRUE,
  
  UNIQUE(city_id, slug)
);

DEVELOPERS & PROJECTS
Developers Table
sqlCREATE TABLE developers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id), -- Linked user account
  
  -- Basic Info
  name VARCHAR(255) NOT NULL,
  slug VARCHAR(255) UNIQUE NOT NULL,
  logo_url VARCHAR(500),
  cover_image_url VARCHAR(500),
  description TEXT,
  
  -- Contact
  website VARCHAR(500),
  email VARCHAR(255),
  phone VARCHAR(20),
  address TEXT,
  
  -- Social
  facebook VARCHAR(255),
  instagram VARCHAR(255),
  linkedin VARCHAR(255),
  
  -- Verification
  is_verified BOOLEAN DEFAULT FALSE,
  verified_at TIMESTAMP,
  license_number VARCHAR(100),
  
  -- Statistics (denormalized for performance)
  total_projects INT DEFAULT 0,
  active_projects INT DEFAULT 0,
  completed_projects INT DEFAULT 0,
  total_sqm_built DECIMAL(12, 2) DEFAULT 0,
  
  -- Status
  status VARCHAR(20) DEFAULT 'pending' CHECK (status IN ('pending', 'active', 'inactive', 'rejected')),
  founded_year INT,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_developers_slug ON developers(slug);
CREATE INDEX idx_developers_status ON developers(status);
Projects Table
sqlCREATE TABLE projects (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  developer_id UUID REFERENCES developers(id) ON DELETE CASCADE,
  city_id UUID REFERENCES cities(id),
  district_id UUID REFERENCES districts(id),
  
  -- Basic Info
  name VARCHAR(255) NOT NULL,
  slug VARCHAR(255) NOT NULL,
  description TEXT,
  
  -- Location
  address VARCHAR(500),
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  nearest_metro_id UUID REFERENCES metro_stations(id),
  metro_distance INT, -- in meters
  
  -- Pricing
  price_min DECIMAL(12, 2),
  price_max DECIMAL(12, 2),
  price_per_sqm_min DECIMAL(10, 2),
  price_per_sqm_max DECIMAL(10, 2),
  currency VARCHAR(3) DEFAULT 'USD',
  
  -- Project Details
  project_type VARCHAR(50) CHECK (project_type IN ('residential', 'commercial', 'mixed', 'cottage')),
  total_buildings INT DEFAULT 1,
  total_floors_min INT,
  total_floors_max INT,
  total_apartments INT,
  available_apartments INT,
  
  -- Areas
  area_min DECIMAL(10, 2),
  area_max DECIMAL(10, 2),
  
  -- Construction
  construction_start DATE,
  construction_end DATE,
  completion_percentage INT DEFAULT 0,
  construction_status VARCHAR(30) CHECK (construction_status IN 
    ('planned', 'under_construction', 'completed', 'on_hold')),
  
  -- Frame Types Available
  has_black_frame BOOLEAN DEFAULT FALSE,
  has_white_frame BOOLEAN DEFAULT FALSE,
  has_green_frame BOOLEAN DEFAULT FALSE,
  
  -- Purchase Terms
  has_installment BOOLEAN DEFAULT FALSE,
  has_mortgage BOOLEAN DEFAULT FALSE,
  has_full_payment_discount BOOLEAN DEFAULT FALSE,
  
  -- Amenities (JSONB for flexibility)
  amenities JSONB DEFAULT '[]',
  -- Example: ["pool", "gym", "parking", "playground", "24h_security", "concierge"]
  
  -- Media
  images JSONB DEFAULT '[]',
  video_url VARCHAR(500),
  virtual_tour_url VARCHAR(500),
  
  -- SEO
  meta_title VARCHAR(255),
  meta_description TEXT,
  
  -- Status
  status VARCHAR(20) DEFAULT 'draft' CHECK (status IN ('draft', 'active', 'inactive', 'archived')),
  is_featured BOOLEAN DEFAULT FALSE,
  featured_until TIMESTAMP,
  
  -- Statistics
  views_count INT DEFAULT 0,
  favorites_count INT DEFAULT 0,
  inquiries_count INT DEFAULT 0,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  published_at TIMESTAMP,
  
  UNIQUE(developer_id, slug)
);

CREATE INDEX idx_projects_developer ON projects(developer_id);
CREATE INDEX idx_projects_city ON projects(city_id);
CREATE INDEX idx_projects_district ON projects(district_id);
CREATE INDEX idx_projects_status ON projects(status);
CREATE INDEX idx_projects_price ON projects(price_min, price_max);
CREATE INDEX idx_projects_location ON projects USING GIST(
  ST_SetSRID(ST_MakePoint(longitude, latitude), 4326)
);
Buildings Table
sqlCREATE TABLE buildings (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  
  name VARCHAR(100), -- "Block A", "Tower 1"
  total_floors INT NOT NULL,
  total_apartments INT,
  
  -- Construction
  completion_date DATE,
  completion_percentage INT DEFAULT 0,
  
  -- Location within project
  position_x DECIMAL(10, 2),
  position_y DECIMAL(10, 2),
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_buildings_project ON buildings(project_id);
Layouts Table
sqlCREATE TABLE layouts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  
  -- Basic Info
  code VARCHAR(50) NOT NULL, -- "A-12", "B-03"
  name VARCHAR(100),
  
  -- Specifications
  rooms INT NOT NULL,
  bedrooms INT,
  bathrooms INT DEFAULT 1,
  total_area DECIMAL(10, 2) NOT NULL,
  living_area DECIMAL(10, 2),
  kitchen_area DECIMAL(10, 2),
  balcony_area DECIMAL(10, 2),
  terrace_area DECIMAL(10, 2),
  
  -- Room Breakdown (JSONB)
  room_breakdown JSONB DEFAULT '[]',
  -- Example: [{"name": "Bedroom 1", "area": 14.5}, {"name": "Living Room", "area": 22.3}]
  
  -- Building Info
  ceiling_height DECIMAL(3, 2),
  orientation JSONB DEFAULT '[]', -- ["south", "east"]
  
  -- Floor Plans
  floor_plan_2d VARCHAR(500),
  floor_plan_3d VARCHAR(500),
  floor_plan_pdf VARCHAR(500),
  floor_plan_svg TEXT, -- Interactive SVG
  
  -- Availability
  floor_range_min INT,
  floor_range_max INT,
  total_units INT DEFAULT 0,
  available_units INT DEFAULT 0,
  
  -- Pricing
  price_min DECIMAL(12, 2),
  price_max DECIMAL(12, 2),
  price_per_sqm_min DECIMAL(10, 2),
  price_per_sqm_max DECIMAL(10, 2),
  
  -- Status
  is_active BOOLEAN DEFAULT TRUE,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  UNIQUE(project_id, code)
);

CREATE INDEX idx_layouts_project ON layouts(project_id);
CREATE INDEX idx_layouts_rooms ON layouts(rooms);
CREATE INDEX idx_layouts_area ON layouts(total_area);
Apartment Units Table
sqlCREATE TABLE apartment_units (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  building_id UUID REFERENCES buildings(id),
  layout_id UUID REFERENCES layouts(id),
  
  -- Identification
  unit_number VARCHAR(20) NOT NULL,
  floor INT NOT NULL,
  
  -- Specifications (may differ slightly from layout)
  total_area DECIMAL(10, 2) NOT NULL,
  price DECIMAL(12, 2) NOT NULL,
  price_per_sqm DECIMAL(10, 2),
  
  -- View & Orientation
  view_type VARCHAR(50), -- "city", "courtyard", "park", "mountain"
  orientation VARCHAR(50),
  
  -- Status
  status VARCHAR(20) DEFAULT 'available' CHECK (status IN 
    ('available', 'reserved', 'sold', 'not_for_sale')),
  reserved_until TIMESTAMP,
  sold_at TIMESTAMP,
  
  -- Special Features
  has_panoramic_windows BOOLEAN DEFAULT FALSE,
  has_terrace BOOLEAN DEFAULT FALSE,
  special_notes TEXT,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_units_project ON apartment_units(project_id);
CREATE INDEX idx_units_layout ON apartment_units(layout_id);
CREATE INDEX idx_units_status ON apartment_units(status);
CREATE INDEX idx_units_floor ON apartment_units(floor);
Construction Progress Table
sqlCREATE TABLE construction_progress (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  
  -- Update Info
  update_date DATE NOT NULL,
  title VARCHAR(255),
  description TEXT,
  completion_percentage INT,
  
  -- Photos
  photos JSONB DEFAULT '[]',
  -- Example: [{"url": "...", "spot_id": "main_facade", "thumbnail": "..."}]
  
  created_by UUID REFERENCES users(id),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_progress_project ON construction_progress(project_id);
CREATE INDEX idx_progress_date ON construction_progress(update_date);
Photo Spots Table
sqlCREATE TABLE photo_spots (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  
  name VARCHAR(100) NOT NULL,
  slug VARCHAR(100) NOT NULL,
  spot_type VARCHAR(30) CHECK (spot_type IN ('ground', 'drone', 'interior')),
  
  -- Position on site map
  position_x DECIMAL(5, 2),
  position_y DECIMAL(5, 2),
  
  sort_order INT DEFAULT 0,
  
  UNIQUE(project_id, slug)
);
Project Documents Table
sqlCREATE TABLE project_documents (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  project_id UUID REFERENCES projects(id) ON DELETE CASCADE,
  
  name VARCHAR(255) NOT NULL,
  document_type VARCHAR(50), -- "permit", "certificate", "brochure", "price_list"
  file_url VARCHAR(500) NOT NULL,
  file_size INT, -- in bytes
  file_type VARCHAR(50), -- "pdf", "doc", "image"
  
  is_public BOOLEAN DEFAULT TRUE,
  sort_order INT DEFAULT 0,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_documents_project ON project_documents(project_id);

SECONDARY MARKET (PROPERTIES)
Property Listings Table
sqlCREATE TABLE property_listings (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  
  -- Listing Type
  listing_type VARCHAR(20) NOT NULL CHECK (listing_type IN ('sale', 'rent', 'daily')),
  property_type VARCHAR(30) NOT NULL CHECK (property_type IN 
    ('apartment', 'house', 'commercial', 'office', 'warehouse', 'land', 'garage')),
  
  -- Basic Info
  title VARCHAR(255),
  description TEXT,
  
  -- Location
  city_id UUID REFERENCES cities(id),
  district_id UUID REFERENCES districts(id),
  address VARCHAR(500),
  hide_address BOOLEAN DEFAULT FALSE,
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  nearest_metro_id UUID REFERENCES metro_stations(id),
  metro_distance INT,
  
  -- Pricing
  price DECIMAL(12, 2) NOT NULL,
  price_per_sqm DECIMAL(10, 2),
  currency VARCHAR(3) DEFAULT 'USD',
  is_negotiable BOOLEAN DEFAULT FALSE,
  
  -- For Rent
  deposit DECIMAL(12, 2),
  utilities_included BOOLEAN DEFAULT FALSE,
  minimum_rental_months INT,
  
  -- Property Details
  rooms INT,
  bedrooms INT,
  bathrooms INT DEFAULT 1,
  total_area DECIMAL(10, 2) NOT NULL,
  living_area DECIMAL(10, 2),
  kitchen_area DECIMAL(10, 2),
  land_area DECIMAL(12, 2), -- For houses/land
  
  -- Building Info
  floor INT,
  total_floors INT,
  building_type VARCHAR(20) CHECK (building_type IN ('new', 'old', 'under_construction')),
  building_year INT,
  building_material VARCHAR(50),
  ceiling_height DECIMAL(3, 2),
  
  -- Condition
  condition VARCHAR(30) CHECK (condition IN 
    ('new_renovation', 'good', 'needs_repair', 'white_frame', 'black_frame', 'green_frame')),
  furniture VARCHAR(20) CHECK (furniture IN ('furnished', 'partially', 'unfurnished')),
  
  -- Amenities (JSONB)
  amenities JSONB DEFAULT '{}',
  -- Example: {"balcony": true, "parking": true, "elevator": true, "gas": true}
  
  -- Media
  images JSONB DEFAULT '[]',
  video_url VARCHAR(500),
  virtual_tour_url VARCHAR(500),
  
  -- Seller Info
  seller_type VARCHAR(20) CHECK (seller_type IN ('owner', 'agent', 'developer')),
  contact_phone VARCHAR(20),
  contact_name VARCHAR(100),
  
  -- Status
  status VARCHAR(20) DEFAULT 'pending' CHECK (status IN 
    ('pending', 'active', 'paused', 'sold', 'rented', 'expired', 'rejected')),
  rejection_reason TEXT,
  
  -- Moderation
  is_verified BOOLEAN DEFAULT FALSE,
  verified_at TIMESTAMP,
  verified_by UUID REFERENCES users(id),
  
  -- Featuring
  is_featured BOOLEAN DEFAULT FALSE,
  featured_until TIMESTAMP,
  
  -- Statistics
  views_count INT DEFAULT 0,
  favorites_count INT DEFAULT 0,
  phone_views_count INT DEFAULT 0,
  
  -- SEO
  slug VARCHAR(255),
  meta_title VARCHAR(255),
  meta_description TEXT,
  
  -- Timestamps
  expires_at TIMESTAMP,
  published_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Indexes
CREATE INDEX idx_listings_user ON property_listings(user_id);
CREATE INDEX idx_listings_type ON property_listings(listing_type, property_type);
CREATE INDEX idx_listings_city ON property_listings(city_id);
CREATE INDEX idx_listings_district ON property_listings(district_id);
CREATE INDEX idx_listings_status ON property_listings(status);
CREATE INDEX idx_listings_price ON property_listings(price);
CREATE INDEX idx_listings_rooms ON property_listings(rooms);
CREATE INDEX idx_listings_area ON property_listings(total_area);
CREATE INDEX idx_listings_location ON property_listings USING GIST(
  ST_SetSRID(ST_MakePoint(longitude, latitude), 4326)
);
CREATE INDEX idx_listings_created ON property_listings(created_at DESC);
CREATE INDEX idx_listings_featured ON property_listings(is_featured, featured_until);

-- Full text search
CREATE INDEX idx_listings_search ON property_listings USING GIN(
  to_tsvector('english', coalesce(title, '') || ' ' || coalesce(description, ''))
);

USER FEATURES
Favorites Table
sqlCREATE TABLE favorites (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  
  -- Polymorphic reference
  item_type VARCHAR(20) NOT NULL CHECK (item_type IN ('project', 'listing', 'layout')),
  item_id UUID NOT NULL,
  
  -- Price tracking
  price_at_save DECIMAL(12, 2),
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  
  UNIQUE(user_id, item_type, item_id)
);

CREATE INDEX idx_favorites_user ON favorites(user_id);
CREATE INDEX idx_favorites_item ON favorites(item_type, item_id);
Saved Searches Table
sqlCREATE TABLE saved_searches (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  
  name VARCHAR(255) NOT NULL,
  search_type VARCHAR(20) NOT NULL, -- 'project', 'sale', 'rent', 'daily'
  
  -- Search Criteria (JSONB)
  filters JSONB NOT NULL,
  
  -- Alert Settings
  alert_enabled BOOLEAN DEFAULT TRUE,
  alert_frequency VARCHAR(20) DEFAULT 'daily' CHECK (alert_frequency IN ('instant', 'daily', 'weekly')),
  alert_channels JSONB DEFAULT '["email"]',
  
  -- Statistics
  matching_count INT DEFAULT 0,
  last_checked_at TIMESTAMP,
  last_alerted_at TIMESTAMP,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_saved_searches_user ON saved_searches(user_id);
CREATE INDEX idx_saved_searches_alert ON saved_searches(alert_enabled, alert_frequency);
Property Alerts Table
sqlCREATE TABLE property_alerts (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  saved_search_id UUID REFERENCES saved_searches(id) ON DELETE CASCADE,
  
  -- Alert Details
  trigger_type VARCHAR(30) NOT NULL CHECK (trigger_type IN 
    ('new_listing', 'price_drop', 'back_on_market', 'status_change')),
  
  -- Reference
  item_type VARCHAR(20) NOT NULL,
  item_id UUID NOT NULL,
  
  -- Alert Content
  title VARCHAR(255),
  message TEXT,
  old_value JSONB,
  new_value JSONB,
  
  -- Status
  is_read BOOLEAN DEFAULT FALSE,
  read_at TIMESTAMP,
  is_sent BOOLEAN DEFAULT FALSE,
  sent_at TIMESTAMP,
  sent_via JSONB DEFAULT '[]',
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_alerts_user ON property_alerts(user_id);
CREATE INDEX idx_alerts_unread ON property_alerts(user_id, is_read) WHERE is_read = FALSE;
Messages Table
sqlCREATE TABLE conversations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  
  -- Related Property (optional)
  property_type VARCHAR(20), -- 'project', 'listing'
  property_id UUID,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE conversation_participants (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  conversation_id UUID REFERENCES conversations(id) ON DELETE CASCADE,
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  
  unread_count INT DEFAULT 0,
  last_read_at TIMESTAMP,
  is_archived BOOLEAN DEFAULT FALSE,
  
  UNIQUE(conversation_id, user_id)
);

CREATE TABLE messages (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  conversation_id UUID REFERENCES conversations(id) ON DELETE CASCADE,
  sender_id UUID REFERENCES users(id),
  
  content TEXT NOT NULL,
  attachments JSONB DEFAULT '[]',
  
  is_system_message BOOLEAN DEFAULT FALSE,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_messages_conversation ON messages(conversation_id);
CREATE INDEX idx_messages_created ON messages(conversation_id, created_at DESC);
CREATE INDEX idx_participants_user ON conversation_participants(user_id);

ADMIN & SYSTEM
Admin Users Table
sqlCREATE TABLE admin_users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(255) NOT NULL,
  
  role VARCHAR(50) NOT NULL,
  permissions JSONB DEFAULT '[]',
  
  is_active BOOLEAN DEFAULT TRUE,
  last_login_at TIMESTAMP,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
Activity Logs Table
sqlCREATE TABLE activity_logs (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  
  -- Actor
  actor_type VARCHAR(20) NOT NULL, -- 'user', 'admin', 'system'
  actor_id UUID,
  
  -- Action
  action VARCHAR(100) NOT NULL,
  resource_type VARCHAR(50),
  resource_id UUID,
  
  -- Details
  details JSONB DEFAULT '{}',
  ip_address INET,
  user_agent TEXT,
  
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_logs_actor ON activity_logs(actor_type, actor_id);
CREATE INDEX idx_logs_resource ON activity_logs(resource_type, resource_id);
CREATE INDEX idx_logs_action ON activity_logs(action);
CREATE INDEX idx_logs_created ON activity_logs(created_at DESC);
System Settings Table
sqlCREATE TABLE system_settings (
  key VARCHAR(100) PRIMARY KEY,
  value JSONB NOT NULL,
  description TEXT,
  updated_by UUID REFERENCES admin_users(id),
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
````
````

---

# **08-API-ENDPOINTS.md**
````markdown
# API ENDPOINTS
## RESTful API Design for Next.js

---

## API STRUCTURE

Base URL: `/api/v1`

Authentication: JWT Bearer Token

Response Format:
```json
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "per_page": 24,
    "total": 1250,
    "total_pages": 53
  }
}
```

Error Format:
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input",
    "details": { ... }
  }
}
```

---

## AUTHENTICATION ENDPOINTS
````
POST   /api/v1/auth/register           # Register new user
POST   /api/v1/auth/login              # Login with email/password
POST   /api/v1/auth/logout             # Logout (invalidate token)
POST   /api/v1/auth/refresh            # Refresh access token
POST   /api/v1/auth/forgot-password    # Request password reset
POST   /api/v1/auth/reset-password     # Reset password with token
POST   /api/v1/auth/verify-email       # Verify email with code
POST   /api/v1/auth/verify-phone       # Verify phone with OTP
POST   /api/v1/auth/resend-verification # Resend verification code
POST   /api/v1/auth/oauth/google       # OAuth with Google
POST   /api/v1/auth/oauth/facebook     # OAuth with Facebook
Request/Response Examples
typescript// POST /api/v1/auth/register
// Request
{
  "email": "user@example.com",
  "phone": "+995555123456",
  "password": "SecurePass123",
  "name": "John Smith",
  "user_type": "buyer"
}

// Response
{
  "success": true,
  "data": {
    "user": {
      "id": "uuid",
      "email": "user@example.com",
      "name": "John Smith",
      "user_type": "buyer",
      "email_verified": false
    },
    "tokens": {
      "access_token": "eyJ...",
      "refresh_token": "eyJ...",
      "expires_in": 3600
    }
  }
}
````

---

## PROJECTS ENDPOINTS
````
GET    /api/v1/projects                # List projects (with filters)
GET    /api/v1/projects/:slug          # Get project detail
GET    /api/v1/projects/:slug/layouts  # Get project layouts
GET    /api/v1/projects/:slug/layouts/:id # Get layout detail
GET    /api/v1/projects/:slug/units    # Get available units
GET    /api/v1/projects/:slug/progress # Get construction progress
GET    /api/v1/projects/:slug/documents # Get project documents
GET    /api/v1/projects/map            # Get projects for map view
GET    /api/v1/projects/featured       # Get featured projects
````

### Query Parameters for Projects List
````
GET /api/v1/projects?
  city=tbilisi
  &districts=vake,saburtalo
  &price_min=50000
  &price_max=150000
  &rooms=1,2,3
  &area_min=40
  &area_max=100
  &completion_year=2024,2025
  &status=under_construction
  &has_pool=true
  &has_installment=true
  &frame_type=white,green
  &sort=price_asc
  &page=1
  &per_page=24
Response Example
typescript// GET /api/v1/projects/:slug
{
  "success": true,
  "data": {
    "id": "uuid",
    "name": "Archi Vashlijvari",
    "slug": "archi-vashlijvari-tbilisi",
    "developer": {
      "id": "uuid",
      "name": "Archi",
      "slug": "archi",
      "logo_url": "...",
      "is_verified": true
    },
    "city": { "id": "uuid", "name": "Tbilisi", "slug": "tbilisi" },
    "district": { "id": "uuid", "name": "Vake", "slug": "vake" },
    "location": {
      "address": "15 Vashlijvari St",
      "latitude": 41.7151,
      "longitude": 44.7827,
      "nearest_metro": { "name": "Medical University", "distance": 800 }
    },
    "pricing": {
      "price_min": 52000,
      "price_max": 250000,
      "price_per_sqm_min": 1100,
      "price_per_sqm_max": 1500,
      "currency": "USD"
    },
    "details": {
      "project_type": "residential",
      "total_buildings": 2,
      "total_floors": "12-18",
      "total_apartments": 450,
      "available_apartments": 180,
      "area_range": "35-150 m²"
    },
    "construction": {
      "status": "under_construction",
      "completion_percentage": 68,
      "start_date": "2022-03-01",
      "end_date": "2025-06-01"
    },
    "features": {
      "frame_types": ["black", "white", "green"],
      "purchase_terms": ["installment", "mortgage", "cash_discount"],
      "amenities": ["pool", "gym", "parking", "24h_security", "playground"]
    },
    "media": {
      "images": [...],
      "video_url": "...",
      "virtual_tour_url": "..."
    },
    "statistics": {
      "views": 12500,
      "favorites": 340,
      "inquiries": 85
    },
    "created_at": "2022-01-15T10:00:00Z",
    "updated_at": "2024-11-28T14:30:00Z"
  }
}
````

---

## PROPERTY LISTINGS ENDPOINTS
````
GET    /api/v1/listings                # List properties (with filters)
GET    /api/v1/listings/:id            # Get property detail
GET    /api/v1/listings/map            # Get listings for map view
GET    /api/v1/listings/similar/:id    # Get similar properties
POST   /api/v1/listings                # Create listing (auth required)
PUT    /api/v1/listings/:id            # Update listing (auth required)
DELETE /api/v1/listings/:id            # Delete listing (auth required)
POST   /api/v1/listings/:id/report     # Report listing
````

### Query Parameters for Listings
````
GET /api/v1/listings?
  type=sale                    # sale, rent, daily
  &property_type=apartment     # apartment, house, commercial, land
  &city=tbilisi
  &districts=vake,saburtalo
  &price_min=50000
  &price_max=150000
  &rooms=1,2,3
  &area_min=40
  &area_max=100
  &floor_min=2
  &floor_max=10
  &not_first_floor=true
  &not_last_floor=true
  &building_type=new
  &condition=new_renovation
  &has_balcony=true
  &has_parking=true
  &has_elevator=true
  &seller_type=owner
  &posted_within=7             # days
  &sort=newest                 # newest, price_asc, price_desc, area_asc
  &page=1
  &per_page=24
````

---

## DEVELOPERS ENDPOINTS
````
GET    /api/v1/developers              # List developers
GET    /api/v1/developers/:slug        # Get developer profile
GET    /api/v1/developers/:slug/projects # Get developer's projects
````

---

## AGENTS ENDPOINTS
````
GET    /api/v1/agents                  # List agents
GET    /api/v1/agents/:id              # Get agent profile
GET    /api/v1/agents/:id/listings     # Get agent's listings
GET    /api/v1/agents/:id/reviews      # Get agent reviews
POST   /api/v1/agents/:id/reviews      # Add review (auth required)
````

---

## USER ENDPOINTS
````
GET    /api/v1/users/me                # Get current user profile
PUT    /api/v1/users/me                # Update profile
PUT    /api/v1/users/me/password       # Change password
PUT    /api/v1/users/me/settings       # Update settings
DELETE /api/v1/users/me                # Delete account

# Favorites
GET    /api/v1/users/me/favorites      # Get favorites
POST   /api/v1/users/me/favorites      # Add to favorites
DELETE /api/v1/users/me/favorites/:id  # Remove from favorites

# Saved Searches
GET    /api/v1/users/me/searches       # Get saved searches
POST   /api/v1/users/me/searches       # Save search
PUT    /api/v1/users/me/searches/:id   # Update saved search
DELETE /api/v1/users/me/searches/:id   # Delete saved search

# Alerts
GET    /api/v1/users/me/alerts         # Get alerts
PUT    /api/v1/users/me/alerts/:id/read # Mark as read
DELETE /api/v1/users/me/alerts/:id     # Delete alert

# My Listings
GET    /api/v1/users/me/listings       # Get my listings
GET    /api/v1/users/me/listings/:id/stats # Get listing statistics
PUT    /api/v1/users/me/listings/:id/status # Update listing status

# Messages
GET    /api/v1/users/me/conversations  # Get conversations
GET    /api/v1/users/me/conversations/:id # Get conversation messages
POST   /api/v1/users/me/conversations  # Start conversation
POST   /api/v1/users/me/conversations/:id/messages # Send message
````

---

## LOOKUP DATA ENDPOINTS
````
GET    /api/v1/cities                  # List cities
GET    /api/v1/cities/:slug/districts  # Get districts in city
GET    /api/v1/cities/:slug/metros     # Get metro stations
GET    /api/v1/amenities               # Get amenities list
GET    /api/v1/property-types          # Get property types
````

---

## UTILITY ENDPOINTS
````
POST   /api/v1/contact                 # Contact form submission
POST   /api/v1/mortgage/calculate      # Calculate mortgage
GET    /api/v1/search/suggestions      # Search autocomplete
POST   /api/v1/upload/image            # Upload image (auth required)
````

---

## ADMIN ENDPOINTS
````
# Dashboard
GET    /api/v1/admin/dashboard         # Get dashboard stats
GET    /api/v1/admin/activity          # Get activity log

# Users
GET    /api/v1/admin/users             # List users
GET    /api/v1/admin/users/:id         # Get user detail
PUT    /api/v1/admin/users/:id         # Update user
PUT    /api/v1/admin/users/:id/status  # Change user status
DELETE /api/v1/admin/users/:id         # Delete user

# Moderation
GET    /api/v1/admin/moderation/queue  # Get moderation queue
PUT    /api/v1/admin/moderation/:id/approve # Approve listing
PUT    /api/v1/admin/moderation/:id/reject  # Reject listing

# Projects
POST   /api/v1/admin/projects          # Create project
PUT    /api/v1/admin/projects/:id      # Update project
DELETE /api/v1/admin/projects/:id      # Delete project

# Developers
GET    /api/v1/admin/developers/applications # Get applications
PUT    /api/v1/admin/developers/:id/approve  # Approve developer
PUT    /api/v1/admin/developers/:id/reject   # Reject developer

# Content
GET    /api/v1/admin/content/pages     # Get pages
PUT    /api/v1/admin/content/pages/:id # Update page
GET    /api/v1/admin/content/seo       # Get SEO templates
PUT    /api/v1/admin/content/seo/:id   # Update SEO template

# Settings
GET    /api/v1/admin/settings          # Get settings
PUT    /api/v1/admin/settings          # Update settings

# Reports
GET    /api/v1/admin/reports/users     # User reports
GET    /api/v1/admin/reports/listings  # Listing reports
GET    /api/v1/admin/reports/revenue   # Revenue reports
````
````

---