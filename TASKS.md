# TASKS – Booking@UMU (Hostel Allocation & Booking Module)

[🏠 README](./README.md) &nbsp;&nbsp;•&nbsp;&nbsp; [🤝 CONTRIBUTIONS](./CONTRIBUTING.md) &nbsp;&nbsp;•&nbsp;&nbsp; [📋 THIS FILE](./TASKS.md)

- [Overview](#overview)
- [Lead Devs (2)](#lead-devs-2)
- [Frontend Devs (4)](#frontend-devs-4)
- [Backend Devs (4)](#backend-devs-4)
- [QA Devs (4)](#qa-devs-4)
- [Design & UX Devs (4)](#design--ux-devs-4)
- [How to Use This File](#how-to-use-this-file)

---

## Overview

This `TASKS.md` tracks work for the **Hostel Allocation & Booking module** and related **admin features** in Booking@UMU.

Use this file to:

- Assign tasks to team members (fill in the `Assignee:` fields).
- Track progress with the checkboxes.
- Coordinate across lead, frontend, backend, QA, and design/UX roles.

---

## Lead Devs (2)

**LD-1 – Architecture & module boundaries**

- [ ] Define hostel domain model (Hostel, Room, Bed, Allocation, Semester, MaintenanceRequest).
- [ ] Document route & URL structure for:
  - [ ] Student hostel views
  - [ ] Admin/warden hostel views
- [ ] Add a short architecture note in `docs/` (or `README` section) for hostel module.

_Assignee (Lead Dev):_ `________________________`

---

**LD-2 – Data model & migrations review**

- [ ] Review proposed migrations for:
  - [ ] `hostels`
  - [ ] `rooms`
  - [ ] `beds`
  - [ ] `semesters`
  - [ ] `allocations`
  - [ ] `maintenance_requests`
- [ ] Confirm constraints: capacity, uniqueness of bed per semester, gender rules.
- [ ] Approve schema before shared DB migrations.

_Assignee (Lead Dev):_ `________________________`

---

**LD-3 – Standards & workflow**

- [ ] Finalize coding standards for Laravel and React (structure, naming).
- [ ] Finalize branch/PR checklist for hostel feature branches.
- [ ] Add or update sections in `CONTRIBUTING.md` for hostel-specific expectations.

_Assignee (Lead Dev):_ `________________________`

---

**LD-4 – Cross-cutting features**

- [ ] Define roles/permissions for hostel module (`warden`, `hostel_admin`, `student`).
- [ ] Decide logging strategy for allocation changes and evictions.
- [ ] Identify notification events (allocation approved, eviction, maintenance resolved) for a later notifications phase.

_Assignee (Lead Dev):_ `________________________`

---

## Frontend Devs (4)

All hostel frontend tasks are in **React + Inertia** under `resources/js/Pages/Hostels/...` and shared components under `resources/js/Components/...`.

### FE Dev 1 – Student hostel discovery & booking

**FE-1.1 – Hostel listing**

- [ ] Create `Hostels/Student/HostelList.jsx` page.
- [ ] Display list of available hostels with basic info (name, location, type, gender, capacity/occupancy).
- [ ] Add filters for gender, price band (if available), and on-campus/off-campus.

_Assignee (Frontend Dev):_ `________________________`

**FE-1.2 – Hostel detail & apply page**

- [ ] Create `Hostels/Student/HostelDetail.jsx`.
- [ ] Show hostel details: rooms types, capacity, simple rules (placeholder content initially).
- [ ] Add “Apply for Hostel” button leading to application form.

_Assignee (Frontend Dev):_ `________________________`

**FE-1.3 – Application form & status**

- [ ] Create `Hostels/Student/ApplicationForm.jsx`.
- [ ] Fields: semester, hostel preference(s), roommate preference (optional), notes.
- [ ] Display application status page: not applied / pending / allocated / waitlisted / rejected.

_Assignee (Frontend Dev):_ `________________________`

---

### FE Dev 2 – Warden/admin dashboards

**FE-2.1 – Hostel overview dashboard**

- [ ] Create `Hostels/Admin/Dashboard.jsx`.
- [ ] Show cards with metrics: total beds, occupied, vacant, waitlisted students, open maintenance issues.
- [ ] Link cards to detailed pages (rooms, allocations, maintenance).

_Assignee (Frontend Dev):_ `________________________`

**FE-2.2 – Room & bed management UI**

- [ ] Create `Hostels/Admin/RoomList.jsx`.
- [ ] Show table of rooms with hostel, room number, capacity, gender, occupancy, status.
- [ ] Add actions for blocking/unblocking beds/rooms (frontend only; wires into backend).

_Assignee (Frontend Dev):_ `________________________`

**FE-2.3 – Allocation management UI**

- [ ] Create `Hostels/Admin/Allocations.jsx`.
- [ ] Table with filters by semester, hostel, student, status.
- [ ] Actions: approve, reject, reassign, evict, mark as checked-in/checked-out (wired to backend routes when ready).
- [ ] Confirm dialogs for destructive actions.

_Assignee (Frontend Dev):_ `________________________`

---

### FE Dev 3 – Admin configuration & maintenance

**FE-3.1 – Semesters management UI**

- [ ] Create `Hostels/Admin/Semesters.jsx`.
- [ ] List, create, edit, and deactivate semesters.
- [ ] Validate date ranges client-side (start < end).

_Assignee (Frontend Dev):_ `________________________`

**FE-3.2 – Hostel metadata management**

- [ ] Create `Hostels/Admin/HostelSettings.jsx`.
- [ ] Manage hostel attributes (name, location, gender policy, capacity metadata).
- [ ] Provide simple form validations and feedback.

_Assignee (Frontend Dev):_ `________________________`

**FE-3.3 – Maintenance requests UI**

- [ ] Student side: simple form to log room/hostel issue (e.g. `Hostels/Student/MaintenanceForm.jsx`).
- [ ] Admin side: `Hostels/Admin/Maintenance.jsx` showing list and statuses.
- [ ] Actions to mark as in-progress/resolved.

_Assignee (Frontend Dev):_ `________________________`

---

### FE Dev 4 – Shared components & UX polish

**FE-4.1 – Layout components**

- [ ] Create shared `AdminLayout` and `StudentLayout` components.
- [ ] Integrate navigation for hostel pages into global nav/sidebar.
- [ ] Ensure mobile responsiveness (collapsing navs).

_Assignee (Frontend Dev):_ `________________________`

**FE-4.2 – Tables, pagination, filters**

- [ ] Create reusable `Table`, `Pagination`, and `FilterBar` components.
- [ ] Use them in hostel pages (allocations, rooms, maintenance, etc.).
- [ ] Ensure accessibility basics (focus, ARIA where needed).

_Assignee (Frontend Dev):_ `________________________`

**FE-4.3 – Forms & feedback components**

- [ ] Standardise form handling (e.g. using Inertia `useForm`).
- [ ] Create shared `FormField`, `Button`, `Modal`, and `Toast/Alert` components.
- [ ] Apply consistent loading and error states across hostel UIs.

_Assignee (Frontend Dev):_ `________________________`

---

## Backend Devs (4)

All hostel backend tasks are in Laravel: models, migrations, factories, policies, controllers, Inertia responses, and tests.

### BE Dev 1 – Core hostel schema & seed data

**BE-1.1 – Migrations & models**

- [ ] Create migrations & models for:
  - [ ] `hostels`
  - [ ] `rooms`
  - [ ] `beds`
  - [ ] `semesters`
- [ ] Define relationships (`Hostel hasMany Room`, `Room hasMany Bed`, etc.).
- [ ] Add foreign keys and constraints.

_Assignee (Backend Dev):_ `________________________`

**BE-1.2 – Seeders & factories**

- [ ] Factories for hostels, rooms, beds, semesters.
- [ ] Seed realistic sample data for dev and QA:
  - [ ] A few hostels (male/female/mixed).
  - [ ] Rooms with capacity.
  - [ ] Semesters (e.g., Semester 1, Semester 2).

_Assignee (Backend Dev):_ `________________________`

---

### BE Dev 2 – Allocation logic & student flows

**BE-2.1 – Allocation schema & model**

- [ ] Create `allocations` migration & model: student_id, bed_id, semester_id, status, timestamps.
- [ ] Add constraints to enforce one active allocation per student per semester.

_Assignee (Backend Dev):_ `________________________`

**BE-2.2 – Allocation service & rules**

- [ ] Implement allocation service class:
  - [ ] Respect bed capacity and gender constraints.
  - [ ] Handle basic waitlist logic (e.g., FIFO when no beds free).
- [ ] Wrap allocation operations in DB transactions.

_Assignee (Backend Dev):_ `________________________`

**BE-2.3 – Student controllers & Inertia responses**

- [ ] Endpoints/Inertia controllers for:
  - [ ] Listing hostels available to current student.
  - [ ] Creating an allocation request/application.
  - [ ] Viewing their allocation history and current status.
- [ ] Pass required props to the React pages.

_Assignee (Backend Dev):_ `________________________`

---

### BE Dev 3 – Admin/warden APIs & policies

**BE-3.1 – Admin controllers**

- [ ] Controllers for hostel admin actions:
  - [ ] List hostels, rooms, beds.
  - [ ] List and filter allocations.
  - [ ] Approve/reject applications.
  - [ ] Reassign bed, evict, check-in/out.

_Assignee (Backend Dev):_ `________________________`

**BE-3.2 – Authorization & policies**

- [ ] Define policies/permissions so:
  - [ ] Only wardens/admins can modify hostels, rooms, beds, allocations.
  - [ ] Students only see their own allocation data.
- [ ] Integrate with existing roles/permissions system.

_Assignee (Backend Dev):_ `________________________`

**BE-3.3 – Semesters & configuration endpoints**

- [ ] CRUD endpoints for semesters (create, update, deactivate).
- [ ] CRUD endpoints for hostels metadata (name, type, gender policy).
- [ ] Validation rules (no overlapping and invalid date ranges).

_Assignee (Backend Dev):_ `________________________`

---

### BE Dev 4 – Maintenance, reporting & performance

**BE-4.1 – Maintenance requests**

- [ ] `maintenance_requests` migration & model (hostel_id, room_id, student_id, description, status).
- [ ] Endpoints for:
  - [ ] Students creating requests.
  - [ ] Wardens updating status and notes.

_Assignee (Backend Dev):_ `________________________`

**BE-4.2 – Reports & analytics**

- [ ] Build services/queries for:
  - [ ] Occupancy per hostel and semester.
  - [ ] Count of waitlisted students per hostel.
  - [ ] Maintenance issues open/closed.

_Assignee (Backend Dev):_ `________________________`

**BE-4.3 – Integrity & performance checks**

- [ ] Ensure key flows use transactions and proper locking (where needed).
- [ ] Add DB indexes for frequent filters (semester_id, hostel_id, status).
- [ ] Document any performance considerations for hostel module.

_Assignee (Backend Dev):_ `________________________`

---

## QA Devs (4)

### QA Dev 1 – Student flows

**QA-1.1 – Test design (student)**

- [ ] Write test cases for:
  - [ ] Viewing hostels and details.
  - [ ] Submitting hostel application.
  - [ ] Viewing allocation status.
- [ ] Include edge cases (full hostels, invalid inputs).

_Assignee (QA Dev):_ `________________________`

**QA-1.2 – Automated tests (student)**

- [ ] Laravel Feature tests for:
  - [ ] Student hostel listing route.
  - [ ] Application submission routes.
  - [ ] Allocation status view.
- [ ] Use factories/seeders to set up data.

_Assignee (QA Dev):_ `________________________`

---

### QA Dev 2 – Admin allocation & constraints

**QA-2.1 – Test design (admin)**

- [ ] Cases for:
  - [ ] Approve/reject applications.
  - [ ] Reassign bed, evict, check-in/out.
  - [ ] Enforcing “one bed per student per semester” and gender rules.

_Assignee (QA Dev):_ `________________________`

**QA-2.2 – Automated tests (admin)**

- [ ] Feature tests for admin routes:
  - [ ] Allocation list filters.
  - [ ] Allocation actions change DB correctly.
- [ ] Assert constraints (no overbooking, no double allocations).

_Assignee (QA Dev):_ `________________________`

---

### QA Dev 3 – Maintenance & validation

**QA-3.1 – Test design (maintenance & config)**

- [ ] Cases for:
  - [ ] Students submitting maintenance requests.
  - [ ] Wardens updating statuses.
  - [ ] Validation on semesters and hostel config forms.

_Assignee (QA Dev):_ `________________________`

**QA-3.2 – Automated tests (maintenance & config)**

- [ ] Feature tests for maintenance endpoints.
- [ ] Tests for invalid configuration (invalid dates, negative capacity).

_Assignee (QA Dev):_ `________________________`

---

### QA Dev 4 – Integration & UI checks

**QA-4.1 – End-to-end scenarios**

- [ ] Scenario: Student applies → Admin allocates → Student checks in → eventual checkout.
- [ ] Scenario: Maintenance request lifecycle (new → in progress → resolved).

_Assignee (QA Dev):_ `________________________`

**QA-4.2 – Manual UI testing**

- [ ] Browser-based checks for hostel pages:
  - [ ] Layout and responsiveness.
  - [ ] Error messages and forms.
- [ ] Basic cross-browser smoke tests.

_Assignee (QA Dev):_ `________________________`

All QA devs:

- [ ] Maintain a shared test plan (link from here or stored under `docs/`).
- [ ] Tag hostel-related tests (e.g. `@hostel`) for selective runs.

---

## Design & UX Devs (4)

### UX/Design Dev 1 – Information architecture & flows

**UX-1.1 – IA for hostel module**

- [ ] Define navigation structure:
  - [ ] Student: “My Hostel”, “Hostel Applications”, “Hostel Maintenance”.
  - [ ] Admin: “Hostel Dashboard”, “Rooms & Beds”, “Allocations”, “Maintenance”, “Settings”.

_Assignee (Design/UX Dev):_ `________________________`

**UX-1.2 – Flow diagrams**

- [ ] Student flows:
  - [ ] Discover → Apply → Wait/Allocate → Move-in/out.
- [ ] Admin flows:
  - [ ] View apps → Allocate → Manage occupancy → Handle maintenance.

_Assignee (Design/UX Dev):_ `________________________`

---

### UX/Design Dev 2 – Student-facing UI

**UX-2.1 – Student UI designs**

- [ ] Figma designs for:
  - [ ] Hostel list & detail pages.
  - [ ] Application form & status.
  - [ ] Maintenance report form.

_Assignee (Design/UX Dev):_ `________________________`

**UX-2.2 – States and feedback**

- [ ] Empty state designs (no hostels, no allocation).
- [ ] Success/error message patterns for forms.

_Assignee (Design/UX Dev):_ `________________________`

---

### UX/Design Dev 3 – Admin dashboards

**UX-3.1 – Admin dashboard designs**

- [ ] Figma layouts for:
  - [ ] Hostel overview dashboard.
  - [ ] Allocations table and actions.
  - [ ] Room & bed management.

_Assignee (Design/UX Dev):_ `________________________`

**UX-3.2 – Components for admin UI**

- [ ] Define patterns for:
  - [ ] Status pills (allocated / waitlisted / maintenance).
  - [ ] Filters, search, and inline actions.

_Assignee (Design/UX Dev):_ `________________________`

---

### UX/Design Dev 4 – Design system & review

**UX-4.1 – Hostel design tokens**

- [ ] Define color coding for hostel-related statuses.
- [ ] Define typography and spacing tokens for hostel pages.

_Assignee (Design/UX Dev):_ `________________________`

**UX-4.2 – Design review process**

- [ ] Set up a simple review checklist for frontend PRs (design alignment).
- [ ] Regularly review and comment on React hostel pages.

_Assignee (Design/UX Dev):_ `________________________`

---

## How to Use This File

- Leads assign names to each `Assignee:` line.
- Each dev checks off `[ ]` → `[x]` as tasks are completed (or you use GitHub Issues and treat this as a reference).
- Keep this file in sync with reality:
  - Add new tasks when the scope grows.
  - Mark blocked items and dependencies if something can’t proceed yet.

For how to contribute changes to Booking@UMU, see **[CONTRIBUTING.md](./CONTRIBUTING.md)**.
