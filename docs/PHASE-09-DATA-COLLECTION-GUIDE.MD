# **09-DATA-COLLECTION-GUIDE.md**
````markdown
# DATA COLLECTION GUIDE
## Instructions for Interns & Data Entry Team

---

## OVERVIEW

This guide provides step-by-step instructions for collecting and entering data 
into the real estate platform. Follow these guidelines carefully to ensure 
data quality and consistency.

---

## 1. CITY & LOCATION DATA

### 1.1 Cities Collection

**Source:** Official Georgian government data, Google Maps

**Fields to Collect:**
| Field | Description | Example |
|-------|-------------|---------|
| name_en | English name | Tbilisi |
| name_ka | Georgian name | თბილისი |
| name_ru | Russian name | Тбилиси |
| slug | URL-friendly | tbilisi |
| latitude :**
1. Tbilisi (Capital - highest priority)
2. Batumi (Tourist hub)
3. Kutaisi (Third largest)
4. Rustavi (Industrial)

**Data Entry Template:**
```csv
name_en,name_ka,name_ru,slug,latitude,longitude
Tbilisi,თბილისი,Тбилиси,tbilisi,41.7151,44.8271
Batumi,ბათუმი,Батуми,batumi,41.6168,41.6367
```

---

### 1.2 Districts Collection

**Source:** Municipal data, Google Maps, korter.ge reference

**Fields to Collect:**
| Field | Description | Example |
|-------|-------------|---------|
| city | Parent city | Tbilisi |
| name_en | English name | Vake |
| name_ka | Georgian name | ვაკე |
| slug | URL-friendly | vake |
| parent_district | If sub-district | (empty or parent) |
| latitude | Center point | 41.7089 |
| longitude | Center point | 44.7456 |

**Tbilisi Districts (Priority Order):**
```
1. Vake (ვაკე) - Premium residential
2. Saburtalo (საბურთალო) - Mixed residential/commercial
3. Vera (ვერა) - Central, historic
4. Mtatsminda (მთაწმინდა) - Central, tourist
5. Old Tbilisi (ძველი თბილისი) - Historic center
6. Dighomi (დიღომი) - New developments
7. Gldani (გლდანი) - Residential
8. Nadzaladevi (ნაძალადევი) - Mixed
9. Isani (ისანი) - Developing
10. Samgori (სამგორი) - Industrial/residential
11. Didube (დიდუბე) - Commercial hub
12. Chugureti (ჩუღურეთი) - Central
13. Krtsanisi (კრწანისი) - Residential
14. Avlabari (ავლაბარი) - Historic
15. Ortachala (ორთაჭალა) - Riverside
```

**Sub-districts Example (Vake):**
```
Vake
├── Vake Park Area
├── Bagebi
├── Vashlijvari
├── Nutsubidze Plateau (Nutsubidze Plato)
└── Tkhinvali
```

---

### 1.3 Metro Stations Collection

**Source:** Tbilisi Metro official, Google Maps

**Tbilisi Metro Stations:**
```
AKHMETELI-VARKETILI LINE (Red):
1. Akhmeteli Theatre (ახმეტელის თეატრი)
2. Sarajishvili (სარაჯიშვილი)
3. Guramishvili (გურამიშვილი)
4. Grmagele (ღრმაღელე)
5. Didube (დიდუბე)
6. Gotsiridze (გოცირიძე)
7. Nadzaladevi (ნაძალადევი)
8. Station Square (სადგურის მოედანი) - Railway
9. Marjanishvili (მარჯანიშვილი)
10. Rustaveli (რუსთაველი)
11. Freedom Square (თავისუფლების მოედანი)
12. Avlabari (ავლაბარი)
13. 300 Aragveli (300 არაგველი)
14. Isani (ისანი)
15. Samgori (სამგორი)
16. Varketili (ვარკეთილი)

SABURTALO LINE (Blue - Branch):
- Medical University (უნივერსიტეტი) - from Nadzaladevi
- Delisi (დელისი)
- Vazha-Pshavela (ვაჟა-ფშაველა)
- State University (სახელმწიფო უნივერსიტეტი)
```

**Fields to Collect:**
```csv
city,name_en,name_ka,slug,line_name,line_color,latitude,longitude
Tbilisi,Rustaveli,რუსთაველი,rustaveli,Akhmeteli-Varketili,#FF0000,41.7010,44.8060
Tbilisi,Freedom Square,თავისუფლების მოედანი,freedom-square,Akhmeteli-Varketili,#FF0000,41.6934,44.8015
```

---

## 2. DEVELOPER DATA COLLECTION

### 2.1 Developer Profile Data

**Source:** Company websites, public registry, korter.ge

**Required Fields:**
| Field | Required | Description | How to Find |
|-------|----------|-------------|-------------|
| name | ✓ | Company name | Official website, registry |
| slug | ✓ | URL-friendly name | Create from name |
| logo_url | ✓ | Company logo | Website, social media |
| description | ✓ | About company | Website "About" page |
| website | ○ | Official website | Google search |
| email | ○ | Contact email | Website contact page |
| phone | ○ | Contact phone | Website contact page |
| address | ○ | Office address | Website, Google Maps |
| founded_year | ○ | Year established | Website, registry |
| facebook | ○ | Facebook page | Search Facebook |
| instagram | ○ | Instagram profile | Search Instagram |

**Quality Checklist:**
- [ ] Logo is high-resolution (min 200x200px)
- [ ] Description is at least 100 words
- [ ] All URLs are valid and working
- [ ] Phone number includes country code (+995)
- [ ] Email format is valid

**Developer Data Template:**
```json
{
  "name": "Archi",
  "slug": "archi",
  "logo_url": "https://...",
  "description": "Archi is one of Georgia's leading development companies...",
  "website": "https://archi.ge",
  "email": "info@archi.ge",
  "phone": "+995 322 123 456",
  "address": "15 Rustaveli Ave, Tbilisi",
  "founded_year": 2005,
  "social": {
    "facebook": "https://facebook.com/archige",
    "instagram": "https://instagram.com/archi.ge"
  }
}
```

---

### 2.2 Developer List (Priority)

**Top 20 Developers to Collect First:**

| # | Developer | Projects | Priority |
|---|-----------|----------|----------|
| 1 | Archi | 50+ | High |
| 2 | m² Group (m2) | 40+ | High |
| 3 | Axis | 30+ | High |
| 4 | Biltmore | 15+ | High |
| 5 | Crystal | 20+ | High |
| 6 | Redix | 25+ | High |
| 7 | Lisi Development | 20+ | High |
| 8 | Dirsi | 15+ | Medium |
| 9 | Ornament | 12+ | Medium |
| 10 | iCo | 10+ | Medium |
| 11 | Premium Development | 10+ | Medium |
| 12 | Element | 8+ | Medium |
| 13 | Gumbati | 10+ | Medium |
| 14 | City Park | 5+ | Medium |
| 15 | Karma Group | 8+ | Medium |
| 16 | Start Development | 6+ | Low |
| 17 | Karvasla | 5+ | Low |
| 18 | Smart Construction | 5+ | Low |
| 19 | Golden House | 4+ | Low |
| 20 | Prime Development | 4+ | Low |

---

## 3. PROJECT DATA COLLECTION

### 3.1 Project Basic Information

**Source:** Developer websites, korter.ge, myhome.ge, ss.ge

**Required Fields:**
| Field | Required | Description |
|-------|----------|-------------|
| name | ✓ | Project name |
| developer | ✓ | Developer company |
| city | ✓ | City location |
| district | ✓ | District/neighborhood |
| address | ✓ | Street address |
| latitude | ✓ | GPS coordinate |
| longitude | ✓ | GPS coordinate |
| project_type | ✓ | residential/commercial/mixed |
| construction_status | ✓ | planned/under_construction/completed |
| completion_percentage | ✓ | 0-100 |
| completion_date | ○ | Expected/actual completion |

**How to Get GPS Coordinates:**
1. Go to Google Maps
2. Search for the project or address
3. Right-click on the exact location
4. Click on the coordinates to copy
5. Format: latitude, longitude (e.g., 41.7151, 44.8271)

### 3.2 Project Pricing Information

**Fields to Collect:**
| Field | Description | Example |
|-------|-------------|---------|
| price_min | Minimum apartment price | 45000 |
| price_max | Maximum apartment price | 350000 |
| price_per_sqm_min | Min price per m² | 1100 |
| price_per_sqm_max | Max price per m² | 1800 |
| currency | Price currency | USD |

**Notes:**
- Always collect prices in USD if available
- If only GEL available, note the exchange rate date
- Update prices monthly

### 3.3 Project Features & Amenities

**Standard Amenities Checklist:**
```
BUILDING AMENITIES:
[ ] Swimming Pool (Indoor)
[ ] Swimming Pool (Outdoor)
[ ] Fitness Center / Gym
[ ] Spa / Sauna
[ ] Underground Parking
[ ] Ground Parking
[ ] 24/7 Security
[ ] Concierge Service
[ ] Lobby
[ ] Elevator(s)
[ ] Commercial Space (Ground Floor)

OUTDOOR AMENITIES:
[ ] Children's Playground
[ ] Garden / Green Space
[ ] BBQ Area
[ ] Tennis Court
[ ] Basketball Court
[ ] Walking Paths
[ ] Pet Area

SERVICES:
[ ] Reception
[ ] Cleaning Service
[ ] Maintenance
[ ] Package Delivery Room
[ ] Bicycle Storage
[ ] EV Charging Stations
```

### 3.4 Project Images Collection

**Required Images:**
1. **Main Facade** (exterior render/photo) - REQUIRED
2. **Aerial/Drone View** - REQUIRED
3. **Lobby/Entrance** - REQUIRED
4. **Amenities** (pool, gym, etc.) - If available
5. **Sample Interiors** - Minimum 3
6. **Location/Surroundings** - 2-3 photos
7. **Construction Progress** - For under-construction projects

**Image Requirements:**
- Minimum resolution: 1200x800 pixels
- Format: JPG or PNG
- File size: Under 2MB each
- No watermarks from other platforms
- Prefer official developer images

**Image Naming Convention:**
```
{project-slug}-{type}-{number}.jpg

Examples:
archi-vashlijvari-facade-01.jpg
archi-vashlijvari-pool-01.jpg
archi-vashlijvari-interior-01.jpg
archi-vashlijvari-interior-02.jpg
archi-vashlijvari-progress-2024-11.jpg
```

---

## 4. LAYOUT DATA COLLECTION

### 4.1 Layout Basic Information

**For each unique floor plan, collect:**

| Field | Required | Description | Example |
|-------|----------|-------------|---------|
| code | ✓ | Layout identifier | A-12, B-03 |
| rooms | ✓ | Total rooms | 2 |
| bedrooms | ○ | Number of bedrooms | 1 |
| bathrooms | ✓ | Number of bathrooms | 1 |
| total_area | ✓ | Total area in m² | 65.5 |
| living_area | ○ | Living area in m² | 48.2 |
| kitchen_area | ○ | Kitchen area in m² | 12.3 |
| balcony_area | ○ | Balcony area in m² | 5.0 |

### 4.2 Room Breakdown Collection

**For detailed layouts, collect each room:**
```json
{
  "layout_code": "A-12",
  "rooms": [
    { "name": "Living Room", "type": "living_room", "area": 22.5 },
    { "name": "Bedroom 1", "type": "bedroom", "area": 14.8 },
    { "name": "Kitchen", "type": "kitchen", "area": 12.3 },
    { "name": "Bathroom", "type": "bathroom", "area": 4.5 },
    { "name": "Hallway", "type": "hallway", "area": 6.2 },
    { "name": "Balcony", "type": "balcony", "area": 5.2 }
  ],
  "total_area": 65.5
}
```

### 4.3 Floor Plan Images

**Required for each layout:**
1. 2D Floor Plan (PNG/JPG) - REQUIRED
2. 3D Floor Plan (if available)
3. PDF Download (if available)

**Floor Plan Quality Requirements:**
- Clean, readable dimensions
- Room labels visible
- Scale indicated
- North arrow (if available)
- Minimum 800x600 pixels

---

## 5. APARTMENT UNIT DATA

### 5.1 Unit Information

**For each individual apartment:**
| Field | Required | Description |
|-------|----------|-------------|
| unit_number | ✓ | Apartment number (e.g., "301") |
| floor | ✓ | Floor number |
| layout_code | ✓ | Reference to layout |
| total_area | ✓ | Exact area (may vary slightly) |
| price | ✓ | Current price |
| status | ✓ | available/reserved/sold |

### 5.2 Bulk Unit Data Template
```csv
project,building,unit_number,floor,layout_code,area,price,status
archi-vashlijvari,Block A,101,1,A-12,65.5,52400,available
archi-vashlijvari,Block A,102,1,A-15,78.2,62560,available
archi-vashlijvari,Block A,201,2,A-12,65.5,55650,reserved
archi-vashlijvari,Block A,202,2,A-15,78.2,66470,sold
```

---

## 6. CONSTRUCTION PROGRESS DATA

### 6.1 Progress Update Collection

**Collect monthly for active projects:**

| Field | Description |
|-------|-------------|
| update_date | Date of update (YYYY-MM-DD) |
| completion_percentage | Overall % complete |
| title | Brief title ("November 2024 Update") |
| description | What was accomplished |
| photos | Progress photos |

### 6.2 Photo Spot Setup

**Standard photo spots for each project:**
1. **Main Facade** - Front view of building
2. **Side View** - Alternative angle
3. **Entrance** - Ground floor entry
4. **Parking Area** - If applicable
5. **Amenities Area** - Pool, gym exterior
6. **Drone View** - Aerial (if available)

---

## 7. DATA ENTRY WORKFLOW

### 7.1 Daily Workflow
```
MORNING (9:00 - 12:00):
1. Check assigned projects list
2. Research developer website
3. Collect basic project information
4. Download available images

AFTERNOON (13:00 - 17:00):
5. Enter data into spreadsheet/system
6. Collect layout information
7. Map GPS coordinates
8. Quality check entries

END OF DAY:
9. Submit completed entries for review
10. Note any missing information
11. Update progress tracker
```

### 7.2 Quality Checklist

Before submitting any entry, verify:

**Project Entry:**
- [ ] All required fields filled
- [ ] GPS coordinates verified on map
- [ ] Prices in correct currency
- [ ] At least 5 images uploaded
- [ ] Developer correctly linked
- [ ] District correctly assigned

**Layout Entry:**
- [ ] Layout code is unique within project
- [ ] Total area equals sum of room areas (±1m²)
- [ ] Floor plan image is clear and readable
- [ ] Price range is logical

**Unit Entry:**
- [ ] Unit number format is consistent
- [ ] Floor number is valid for building
- [ ] Price matches layout price range
- [ ] Status is current

---

## 8. DATA SOURCES

### 8.1 Primary Sources

| Source | URL | Data Available |
|--------|-----|----------------|
| korter.ge | https://korter.ge | Projects, layouts, prices |
| myhome.ge | https://myhome.ge | Secondary listings |
| ss.ge | https://ss.ge | Secondary listings |
| Developer websites | Various | Official project data |

### 8.2 Secondary Sources

| Source | Use For |
|--------|---------|
| Google Maps | GPS coordinates, distances |
| Facebook | Developer social info |
| Public Registry | Company verification |
| News articles | Project announcements |

---

## 9. COMMON ISSUES & SOLUTIONS

### 9.1 Missing Data

| Issue | Solution |
|-------|----------|
| No price available | Mark as "Price on request", note in comments |
| No GPS coordinates | Use district center coordinates, flag for review |
| Low-quality images | Request from developer, use placeholder |
| Incomplete layouts | Enter available data, mark incomplete |

### 9.2 Data Conflicts

| Issue | Solution |
|-------|----------|
| Different prices on sources | Use developer website price |
| Different area measurements | Use developer's official data |
| Outdated information | Add date stamp, flag for update |

---

## 10. REPORTING & COMMUNICATION

### 10.1 Daily Report Template
```
DATE: [YYYY-MM-DD]
COLLECTOR: [Name]

COMPLETED TODAY:
- Projects entered: [number]
- Layouts entered: [number]
- Units entered: [number]
- Images uploaded: [number]

ISSUES ENCOUNTERED:
- [Issue 1]
- [Issue 2]

BLOCKED ITEMS (need help):
- [Item 1]
- [Item 2]

TOMORROW'S PLAN:
- [Task 1]
- [Task 2]
```

### 10.2 Escalation Path

1. **Data Questions** → Team Lead
2. **Technical Issues** → Tech Support
3. **Missing Source Data** → Research Team
4. **Quality Disputes** → QA Manager
