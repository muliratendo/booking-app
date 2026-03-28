# Booking@UMU — Hostel Module Architecture

[🏠 README](./README.md) &nbsp;&nbsp;•&nbsp;&nbsp; [🤝 CONTRIBUTIONS](./CONTRIBUTING.md) &nbsp;&nbsp;•&nbsp;&nbsp; [📋 TASKS](./docs/TASKS.md) &nbsp;&nbsp;•&nbsp;&nbsp; [📐 THIS FILE](./docs/ARCHITECTURE.md)

---

## Table of Contents

1. [Module Overview](#1-module-overview)
2. [Technology Stack](#2-technology-stack)
3. [Domain Model](#3-domain-model)
   - [Entity Definitions](#31-entity-definitions)
   - [Relationships](#32-relationships)
   - [Entity Relationship Diagram](#33-entity-relationship-diagram)
4. [Business Rules & Constraints](#4-business-rules--constraints)
5. [Allocation Status Lifecycle](#5-allocation-status-lifecycle)
6. [Route & URL Structure](#6-route--url-structure)
   - [Student Portal](#61-student-portal-prefix-student)
   - [Admin / Warden Portal](#62-admin--warden-portal-prefix-admin)
7. [Directory Structure](#7-directory-structure)
8. [Key Design Decisions](#8-key-design-decisions)
9. [Out of Scope (This Phase)](#9-out-of-scope-this-phase)

---

## 1. Module Overview

The **Hostel Allocation Module** manages end-to-end student accommodation workflows
within Booking@UMU. It handles:

- Student hostel applications per academic semester
- Room and bed allocation (respecting capacity and gender rules)
- Occupancy lifecycle: application → allocation → check-in → checkout
- Waitlist management when hostels are full
- Maintenance request tracking per room/hostel
- Admin dashboards and reports for wardens and hostel administrators

There are **two distinct user portals**:

| Portal | Role(s) | URL Prefix |
|---|---|---|
| Student Portal | `student` | `/student/...` |
| Admin / Warden Portal | `admin`, `warden` | `/admin/...` |

---

## 2. Technology Stack

| Layer | Technology |
|---|---|
| Frontend | React 19 + Inertia.js |
| Backend | Laravel 12 (PHP 8.3) |
| Database | MySQL 8 |
| Auth | Laravel Breeze / Sanctum (session-based via Inertia) |
| Styling | Tailwind CSS |
| Testing | PHPUnit (Feature tests), Factories/Seeders |

All page rendering uses **Inertia.js server-driven routing** — there is no separate
REST API for frontend consumption. Controllers return `Inertia::render(...)` responses
with typed props passed directly to React components.

---

## 3. Domain Model

### 3.1 Entity Definitions

#### `Hostel`

A physical accommodation building on campus.

| Column | Type | Notes |
|---|---|---|
| `id` | `bigint PK` | Auto-increment |
| `name` | `varchar(255)` | e.g. "Micheal Hall" |
| `code` | `varchar(255) UNIQUE` | e.g. "HALL-ML" |
| `gender_policy` | `enum(male,female,mixed)` | Default policy for all rooms |
| `capacity` | `int unsigned` | Stored for fast reads (sum of all room capacities) |
| `description` | `text nullable` | |
| `location` | `varchar nullable` | Building location on campus or off-campus |
| `is_active` | `boolean` | Default: `true` |
| `feature_image_path` | `varchar nullable` | Path to feature image |
| `deleted_at` | `timestamp nullable` | Soft delete |

---

#### `Room`

A room within a hostel, containing one or more beds.

| Column | Type | Notes |
|---|---|---|
| `id` | `bigint PK` | |
| `hostel_id` | `bigint FK → hostels.id` | Cascades on delete |
| `room_number` | `varchar(255)` | Unique within hostel |
| `capacity` | `int unsigned` | Number of beds |
| `gender_override` | `enum(male,female,mixed) nullable` | Overrides hostel gender policy when set |
| `status` | `enum(active,maintenance,closed)` | Default: `active` |
| `deleted_at` | `timestamp nullable` | Soft delete |

> **Constraint:** `UNIQUE(hostel_id, room_number)`

---

#### `Bed`

The atomic allocation unit within a room.

| Column | Type | Notes |
|---|---|---|
| `id` | `bigint PK` | |
| `room_id` | `bigint FK → rooms.id` | Cascades on delete |
| `label` | `varchar(255)` | e.g. "Bed A", "Top Bunk" |
| `status` | `enum(available,occupied,maintenance)` | Default: `available` |
| `deleted_at` | `timestamp nullable` | Soft delete |

> **Constraint:** `UNIQUE(room_id, label)`

---

#### `AcademicTerm` _(Semester)_

Scopes all allocations and applications to a specific university term.

| Column | Type | Notes |
|---|---|---|
| `id` | `bigint PK` | |
| `name` | `varchar(255)` | e.g. "Semester I 2025/2026" |
| `academic_year` | `varchar(255)` | e.g. "2025/2026" |
| `semester` | `enum(I,II)` | e.g. "I" or "II" |
| `start_date` | `date` | |
| `end_date` | `date` | |
| `application_open` | `date nullable` | When students can apply |
| `application_close` | `date nullable` | Application deadline |
| `is_active` | `boolean` | Only one term active at a time |

> **Constraint:** `UNIQUE(academic_year, semester)`

---

#### `Student`

A registered student linked to a system user account.

| Column | Type | Notes |
|---|---|---|
| `id` | `bigint PK` | |
| `user_id` | `bigint FK → users.id` | Cascades on delete |
| `student_number` | `varchar UNIQUE` | e.g. "2025-B00-0000" |
| `first_name` | `varchar(255)` | |
| `last_name` | `varchar(255)` | |
| `gender` | `enum(male,female)` | Drives gender policy checks |
| `program` | `varchar(255)` | Degree programme |
| `year_of_study` | `smallint unsigned` | |
| `phone` | `varchar nullable` | |
| `email` | `varchar nullable` | |
| `deleted_at` | `timestamp nullable` | Soft delete |

---

#### `Application`

A student's request to be housed in a given semester.

| Column | Type | Notes |
|---|---|---|
| `id` | `bigint PK` | |
| `student_id` | `bigint FK → students.id` | Cascades on delete |
| `academic_term_id` | `bigint FK → academic_terms.id` | |
| `status` | `enum(pending,allocated,waitlisted,rejected)` | Default: `pending` |
| `preferences` | `json nullable` | `{ hostel_ids: [], notes: "" }` |
| `notes` | `text nullable` | Student comments |
| `submitted_at` | `timestamp nullable` | |
| `deleted_at` | `timestamp nullable` | Soft delete |

> **Constraint:** `UNIQUE(student_id, academic_term_id)` — one application per student per term.

---

#### `Allocation`

Links a student to a specific bed for a specific semester.

| Column | Type | Notes |
|---|---|---|
| `id` | `bigint PK` | |
| `student_id` | `bigint FK → students.id` | |
| `bed_id` | `bigint FK → beds.id` | |
| `academic_term_id` | `bigint FK → academic_terms.id` | |
| `application_id` | `bigint FK nullable → applications.id` | Null if manually assigned |
| `status` | `enum(allocated,waitlisted,checked_in,checked_out,rejected)` | Default: `allocated` |
| `waitlist_position` | `smallint unsigned nullable` | Position in waitlist queue |
| `checked_in_at` | `timestamp nullable` | |
| `checked_out_at` | `timestamp nullable` | |
| `admin_notes` | `text nullable` | Warden remarks |
| `deleted_at` | `timestamp nullable` | Soft delete |

> **Core constraint:** `UNIQUE(bed_id, academic_term_id)` — one active allocation per bed per term.
> This is enforced at **both** the database level (unique index) and application level (AllocationService).

---

#### `MaintenanceRequest`

A logged issue against a hostel room, submitted by a student or warden.

| Column | Type | Notes |
|---|---|---|
| `id` | `bigint PK` | |
| `hostel_id` | `bigint FK → hostels.id` | |
| `room_id` | `bigint FK → rooms.id` | |
| `student_id` | `bigint FK nullable → students.id` | Null if logged by admin |
| `title` | `varchar(255)` | Brief issue title |
| `description` | `text` | Full issue description |
| `status` | `enum(open,in_progress,resolved,closed)` | Default: `open` |
| `priority` | `enum(low,medium,high,urgent)` | Default: `medium` |
| `resolved_at` | `timestamp nullable` | |
| `warden_notes` | `text nullable` | Admin follow-up notes |
| `created_at` / `updated_at` | `timestamps` | |

---

### 3.2 Relationships

```
Hostel        has many  →  Room
Room          has many  →  Bed
Room          belongs to → Hostel
Bed           belongs to → Room
Bed           has many  →  Allocation

Student       belongs to → User
Student       has many  →  Application
Student       has many  →  Allocation
Student       has many  →  MaintenanceRequest

AcademicTerm  has many  →  Application
AcademicTerm  has many  →  Allocation

Application   belongs to → Student
Application   belongs to → AcademicTerm
Application   has one   →  Allocation

Allocation    belongs to → Student
Allocation    belongs to → Bed
Allocation    belongs to → AcademicTerm
Allocation    belongs to → Application (nullable)

MaintenanceRequest  belongs to → Hostel
MaintenanceRequest  belongs to → Room
MaintenanceRequest  belongs to → Student (nullable)
```

---

### 3.3 Entity Relationship Diagram

```
┌─────────────┐       ┌──────────────┐       ┌────────┐
│   Hostel    │──────<│     Room     │──────<│  Bed   │
│─────────────│       │──────────────│       │────────│
│ id          │       │ id           │       │ id     │
│ name        │       │ hostel_id FK │       │room_id │
│ code        │       │ room_number  │       │ label  │
│gender_policy│       │ capacity     │       │ status │
│ capacity    │       │gender_override       └────┬───┘
│ is_active   │       │ status       │            │
└──────┬──────┘       └──────────────┘            │
       │                                          │
       │ (hostel_id)                              │ (bed_id)
       │                                          ▼
┌──────┴────────────┐               ┌─────────────────────┐
│ MaintenanceRequest│               │     Allocation      │
│───────────────────│               │─────────────────────│
│ id                │               │ id                  │
│ hostel_id FK      │               │ student_id FK       │
│ room_id FK        │               │ bed_id FK           │
│ student_id FK     │               │ academic_term_id FK │
│ title /description│               │ application_id FK   │
│ status / priority │               │ status              │
└───────────────────┘               │ waitlist_position   │
                                    │ checked_in_at       │
┌─────────────┐   ┌──────────────┐  └──────────┬──────────┘
│AcademicTerm │   │  Application │             │
│─────────────│   │──────────────│             │
│ id          │──<│academic_term │             │
│ name        │   │ id           │─────────────┘
│ semester    │   │ student_id FK│
│ is_active   │   │ status       │
└─────────────┘   │ preferences  │
                  └──────┬───────┘
                         │ (student_id)
                  ┌──────┴───────┐     ┌──────────┐
                  │   Student    │────>│   User   │
                  │──────────────│     │──────────│
                  │ id           │     │ id       │
                  │ user_id FK   │     │ email    │
                  │student_number│     │ password │
                  │ gender       │     │ role     │
                  └──────────────┘     └──────────┘
```

---

## 4. Business Rules & Constraints

These rules are enforced at **two levels**: the database (migrations/indexes) and
the application layer (AllocationService, Policies).

| # | Rule | Enforcement Layer |
|---|---|---|
| BR-1 | A bed can have **at most one active allocation per semester** | DB unique index + AllocationService |
| BR-2 | A student can submit **only one application per semester** | DB unique index on `(student_id, academic_term_id)` |
| BR-3 | A student may only be allocated to a hostel/room whose gender policy is **compatible with their gender** (`male` or `female` must match; `mixed` always passes) | AllocationService + Laravel Policy |
| BR-4 | Room-level `gender_override` takes **priority** over hostel-level `gender_policy` when set | `Room::effectiveGenderPolicy()` helper |
| BR-5 | Total active allocations per room per semester must **not exceed** `room.capacity` | AllocationService capacity check |
| BR-6 | Applications can only be submitted while `academic_term.application_open ≤ today ≤ application_close` | `AcademicTerm::isApplicationOpen()` + controller gate |
| BR-7 | Only `allocated` → can transition to `checked_in` | `Allocation::checkIn()` guard |
| BR-8 | Only `checked_in` → can transition to `checked_out`; checkout auto-promotes next waitlisted student | `Allocation::checkOut()` + `promoteNextWaitlisted()` |
| BR-9 | Wardens and admins can **manually override** an allocation (bypass preference rules) with a required `admin_notes` entry | Admin controller + Policy |

---

## 5. Allocation Status Lifecycle

```
                          ┌──────────────────────┐
                          │    not_applied       │  (student has no application)
                          └──────────┬───────────┘
                                     │ student submits application
                                     ▼
                          ┌──────────────────────┐
                          │      pending         │  (application submitted, awaiting processing)
                          └──────────┬───────────┘
                     ┌───────────────┼────────────────┐
                     │               │                │
                     ▼               ▼                ▼
              ┌────────────┐  ┌────────────┐  ┌────────────┐
              │ allocated  │  │ waitlisted │  │  rejected  │
              └─────┬──────┘  └─────┬──────┘  └────────────┘
                    │               │ bed freed (checkout/rejection)
                    │               └──────────────┐
                    │                              ▼
                    │                       ┌────────────┐
                    │                       │ allocated  │  (promoted from waitlist)
                    │                       └─────┬──────┘
                    ▼                             │
            ┌──────────────┐                      │
            │  checked_in  │◄─────────────────────┘
            └──────┬───────┘
                   │ student physically checks out
                   ▼
            ┌──────────────┐
            │ checked_out  │
            └──────────────┘
```

---

## 6. Route & URL Structure

All routes require authentication (`auth` middleware).
Role gates use the `role:` middleware (e.g. `role:student`, `role:admin`).

### 6.1 Student Portal (prefix: `/student`)

| Method | URI | Route Name | Inertia Page | Description |
|---|---|---|---|---|
| `GET` | `/student/dashboard` | `student.dashboard` | `Student/Dashboard` | Status card, current allocation summary |
| `GET` | `/student/hostels` | `student.hostels.index` | `Student/Hostels/Index` | Browse available hostels |
| `GET` | `/student/hostels/{hostel}` | `student.hostels.show` | `Student/Hostels/Show` | Hostel detail + Apply button |
| `GET` | `/student/applications` | `student.applications.index` | `Student/Applications/Index` | List of student's applications |
| `GET` | `/student/applications/create` | `student.applications.create` | `Student/Applications/Create` | Application form |
| `POST` | `/student/applications` | `student.applications.store` | — | Submit application |
| `GET` | `/student/applications/{application}` | `student.applications.show` | `Student/Applications/Show` | Application status detail |
| `GET` | `/student/allocation` | `student.allocation.show` | `Student/Allocation/Show` | Current bed/room/hostel assignment |
| `POST` | `/student/allocation/{allocation}/check-in` | `student.allocation.check-in` | — | Confirm physical check-in |
| `GET` | `/student/maintenance` | `student.maintenance.index` | `Student/Maintenance/Index` | Student's maintenance requests |
| `GET` | `/student/maintenance/create` | `student.maintenance.create` | `Student/Maintenance/Create` | Log a new maintenance issue |
| `POST` | `/student/maintenance` | `student.maintenance.store` | — | Submit maintenance request |
| `GET` | `/student/profile` | `student.profile.edit` | `Student/Profile/Edit` | Edit profile |
| `PATCH` | `/student/profile` | `student.profile.update` | — | Save profile changes |

---

### 6.2 Admin / Warden Portal (prefix: `/admin`)

| Method | URI | Route Name | Inertia Page | Description |
|---|---|---|---|---|
| `GET` | `/admin/dashboard` | `admin.dashboard` | `Admin/Dashboard` | Metric cards: beds, occupied, vacant, waitlisted, maintenance |
| `GET` | `/admin/hostels` | `admin.hostels.index` | `Admin/Hostels/Index` | List all hostels |
| `GET` | `/admin/hostels/create` | `admin.hostels.create` | `Admin/Hostels/Create` | New hostel form |
| `POST` | `/admin/hostels` | `admin.hostels.store` | — | Save hostel |
| `GET` | `/admin/hostels/{hostel}` | `admin.hostels.show` | `Admin/Hostels/Show` | Hostel detail + occupancy |
| `GET` | `/admin/hostels/{hostel}/edit` | `admin.hostels.edit` | `Admin/Hostels/Edit` | Edit hostel |
| `PATCH` | `/admin/hostels/{hostel}` | `admin.hostels.update` | — | Update hostel |
| `DELETE` | `/admin/hostels/{hostel}` | `admin.hostels.destroy` | — | Soft-delete hostel |
| `GET` | `/admin/hostels/{hostel}/rooms` | `admin.hostels.rooms.index` | `Admin/Rooms/Index` | Rooms in a hostel |
| `GET` | `/admin/rooms/create` | `admin.rooms.create` | `Admin/Rooms/Create` | New room form |
| `POST` | `/admin/rooms` | `admin.rooms.store` | — | Save room |
| `GET` | `/admin/rooms/{room}/edit` | `admin.rooms.edit` | `Admin/Rooms/Edit` | Edit room |
| `PATCH` | `/admin/rooms/{room}` | `admin.rooms.update` | — | Update room |
| `GET` | `/admin/rooms/{room}/beds` | `admin.rooms.beds.index` | `Admin/Beds/Index` | Beds in a room |
| `POST` | `/admin/beds` | `admin.beds.store` | — | Add bed to room |
| `PATCH` | `/admin/beds/{bed}` | `admin.beds.update` | — | Update bed (status/label) |
| `GET` | `/admin/students` | `admin.students.index` | `Admin/Students/Index` | Student registry |
| `GET` | `/admin/students/{student}` | `admin.students.show` | `Admin/Students/Show` | Student detail + history |
| `GET` | `/admin/academic-terms` | `admin.academic-terms.index` | `Admin/AcademicTerms/Index` | Manage semesters |
| `POST` | `/admin/academic-terms` | `admin.academic-terms.store` | — | Create semester |
| `PATCH` | `/admin/academic-terms/{term}` | `admin.academic-terms.update` | — | Update semester |
| `GET` | `/admin/applications` | `admin.applications.index` | `Admin/Applications/Index` | All applications (filterable) |
| `PATCH` | `/admin/applications/{application}/approve` | `admin.applications.approve` | — | Approve application |
| `PATCH` | `/admin/applications/{application}/reject` | `admin.applications.reject` | — | Reject application |
| `GET` | `/admin/allocations` | `admin.allocations.index` | `Admin/Allocations/Index` | All allocations |
| `POST` | `/admin/allocations/run` | `admin.allocations.run` | — | Trigger batch allocation job |
| `PATCH` | `/admin/allocations/{allocation}/override` | `admin.allocations.override` | — | Manual bed reassignment |
| `PATCH` | `/admin/allocations/{allocation}/check-out` | `admin.allocations.check-out` | — | Record checkout |
| `GET` | `/admin/waitlist` | `admin.waitlist.index` | `Admin/Waitlist/Index` | Waitlisted students |
| `PATCH` | `/admin/waitlist/{allocation}/promote` | `admin.waitlist.promote` | — | Manually promote student |
| `GET` | `/admin/maintenance` | `admin.maintenance.index` | `Admin/Maintenance/Index` | All maintenance requests |
| `PATCH` | `/admin/maintenance/{request}/update-status` | `admin.maintenance.update-status` | — | Update issue status |
| `GET` | `/admin/reports/occupancy` | `admin.reports.occupancy` | `Admin/Reports/Occupancy` | Occupancy per hostel/semester |
| `GET` | `/admin/reports/export` | `admin.reports.export` | — | Download CSV/PDF |

---

## 7. Directory Structure

```
app/
├── Models/
│   ├── Hostel.php
│   ├── Room.php
│   ├── Bed.php
│   ├── AcademicTerm.php
│   ├── Student.php
│   ├── Application.php
│   ├── Allocation.php
│   └── MaintenanceRequest.php
│
├── Http/Controllers/
│   ├── Student/
│   │   ├── DashboardController.php
│   │   ├── HostelController.php
│   │   ├── ApplicationController.php
│   │   ├── AllocationController.php
│   │   └── MaintenanceController.php
│   └── Admin/
│       ├── DashboardController.php
│       ├── HostelController.php
│       ├── RoomController.php
│       ├── BedController.php
│       ├── StudentController.php
│       ├── AcademicTermController.php
│       ├── ApplicationController.php
│       ├── AllocationController.php
│       ├── WaitlistController.php
│       ├── MaintenanceController.php
│       └── ReportController.php
│
├── Services/
│   ├── AllocationService.php       # Core allocation engine + waitlist
│   └── OccupancyReportService.php  # Dashboard metrics + reports
│
└── Policies/
    ├── HostelPolicy.php
    ├── AllocationPolicy.php
    └── ApplicationPolicy.php

resources/js/
├── Pages/
│   ├── Student/
│   │   ├── Dashboard.jsx
│   │   ├── Hostels/Index.jsx
│   │   ├── Hostels/Show.jsx
│   │   ├── Applications/Index.jsx
│   │   ├── Applications/Create.jsx
│   │   ├── Applications/Show.jsx
│   │   ├── Allocation/Show.jsx
│   │   └── Maintenance/
│   │       ├── Create.jsx
│   │       └── Index.jsx
│   └── Admin/
│       ├── Dashboard.jsx
│       ├── Hostels/Index.jsx
│       ├── Rooms/Index.jsx
│       ├── Beds/Index.jsx
│       ├── Students/
│       │   ├── Show.jsx
│       │   └── Index.jsx
│       ├── AcademicTerms/Index.jsx
│       ├── Applications/Index.jsx
│       ├── Allocations/Index.jsx
│       ├── Waitlist/Index.jsx
│       ├── Maintenance/Index.jsx
│       └── Reports/Occupancy.jsx
│
└── Components/
    ├── Layouts/
    │   ├── AdminLayout.jsx
    │   └── StudentLayout.jsx
    ├── Table.jsx
    ├── Pagination.jsx
    ├── FilterBar.jsx
    ├── FormField.jsx
    ├── Modal.jsx
    └── StatusPill.jsx

database/
├── migrations/
│   ├── ..._create_hostels_table.php
│   ├── ..._create_rooms_table.php
│   ├── ..._create_beds_table.php
│   ├── ..._create_academic_terms_table.php
│   ├── ..._create_students_table.php
│   ├── ..._create_applications_table.php
│   ├── ..._create_allocations_table.php
│   └── ..._create_maintenance_requests_table.php
└── seeders/
    └── HostelSeeder.php

routes/
├── web.php                # Registers all student + admin route groups
└── (split into web-student.php, web-admin.php if file grows large)
```

---

## 8. Key Design Decisions

| Decision | Rationale |
|---|---|
| **Bed is the atomic allocation unit** | Rooms have capacity > 1; allocating to a bed (not a room) prevents overbooking and supports bunk/shared room configurations |
| **Gender override at room level** | Allows a hostel to be `male` but have a nurse/guest room configured as `mixed`, without changing the entire hostel policy |
| **Soft deletes on all core entities** | Hostel records are legally and administratively significant; hard deletion would destroy audit trails |
| **UNIQUE(bed_id, academic_term_id)** | Last-line-of-defense DB constraint — prevents double allocation even if application logic has a concurrency bug |
| **AllocationService is a plain PHP class** | Not a Laravel Job (yet) — keeps it synchronous and testable now; can be wrapped in a Job for async batch processing in a later phase |
| **Inertia over REST API** | The team has no separate mobile app requirement. Inertia keeps the stack simpler (no JSON serialization layer, no CORS, no token auth for web) |
| **`preferences` stored as JSON** | Student preferences (hostel_ids, roommate name, notes) vary and will evolve; JSON avoids premature normalization |
| **One active AcademicTerm at a time** | Enforced at the application layer (`is_active = true` on only one row) — prevents allocation confusion across overlapping terms |

---

## 9. Out of Scope (This Phase)

The following are **noted but deferred** to a later sprint:

- Push / email notifications (allocation approved, eviction, maintenance resolved)
- Integration with Makerere University SIS (student data sync)
- Payment gateway for hostel fees
- Mobile application (Android/iOS)
- Preference-scoring algorithm (beyond FIFO waitlist)
- Photo uploads for maintenance requests
- Multi-campus hostel support

## 10. Media & File Storage

### 10.1 Hall / Hostel Images

- Hall and hostel images are stored on the **filesystem**, not as blobs in MySQL.
- Storage uses Laravel’s filesystem disks (default: `public` disk).
- The `hostels` table stores only a **relative path** to the image:

  - Column: `feature_image_path VARCHAR(255) NULL`
  - Example value: `hostels/lumumba-hall.jpg`

- Frontend uses `Storage::url(feature_image_path)` to render images.

### 10.2 Storage Location & Conventions

- All hostel images are uploaded to: `storage/app/public/hostels/`.
- A symlink `public/storage → storage/app/public` is created via:

  ```bash
  php artisan storage:link
  ```

- File naming convention:
  - `hostels/{hostel_code}-{size}.{ext}`
  - Example: `hostels/HALL-A-cover.jpg`, `hostels/HALL-A-thumb.jpg`.

### 10.3 Future Extensions

- In future, images may be moved to a cloud disk (e.g. s3) by reconfiguring `config/filesystems.php` and updating upload logic to use:

    ```php
    $path = $request->file('image')->store('hostels', 'public');
    ```

- The database schema remains unchanged because only the relative path is stored.
