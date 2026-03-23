![Booking@UMU preview](assets/preview.png)

---

[🏠 THIS FILE](./README.md) &nbsp;&nbsp;•&nbsp;&nbsp; [🤝 CONTRIBUTIONS](./CONTRIBUTING.md) &nbsp;&nbsp;•&nbsp;&nbsp; [📋 TASKS](./docs/TASKS.md)

---

Booking@UMU is a modular, multi-institution booking and allocation platform for **Uganda Martyrs University** and its affiliates — including on-campus hostels and Nkozi Hospital. It unifies academic resource booking, hostel allocation, and hospital services into a single **Laravel + React (Inertia)** application.


| Join the wait list: [Booking@UMU Wait List](https://muliratendo.github.io/booking-umu/#notify) [![Launch subscription QR](assets/qr-booking-umu.png)](https://muliratendo.github.io/booking-umu/#notify) |
|:------------------------------------------------------------------------------------------------:|

---

## Table of Contents

- [Project Overview](#project-overview)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Running the App](#running-the-app)
- [Branching and Workflow](#branching-and-workflow)
- [Tasks and Team Work](#tasks-and-team-work)
- [Contributing Guidelines](#contributing-guidelines)
- [License](#license)

---

## Project Overview

The Booking@UMU Platform solves fragmented booking and allocation workflows across three core domains:

| Domain | Description |
|---|---|
| **Academic Booking** | Rooms, labs, equipment, and conference hall sessions |
| **Hostel Allocation** | Semester-based room/bed assignments and occupancy tracking |
| **Hospital Services** | Appointments, asynchronous consultations, and emergency escalation |

The goal is a maintainable, open-source codebase that can be extended by future student teams and integrated with UMU and Nkozi Hospital systems.

---

## Tech Stack

### Backend
- **Laravel** (PHP 8+)
- **MySQL**
- **Inertia.js** (server-side adapter)

### Frontend
- **React** + Inertia.js (SPA-style pages inside Laravel)
- **Vite** (bundler)
- HTML/CSS (with optional CSS framework)

### Infrastructure / Dev
- Local LEMP stack (Linux, Nginx, MySQL, PHP-FPM)
- Git + GitHub (feature-branch workflow with PR reviews)

---

## Repository Structure

```text
booking-umu/
├──booking/
│  ├──app/                # Laravel backend (models, controllers, policies, etc.)
│  ├──bootstrap/
│  ├──config/
│  ├──database/           # Migrations, seeders, factories
│  ├──public/             # Public web root
│  ├──resources/
│  │  ├──js/
│  │  │  ├──Pages/          # React Inertia pages
│  │  │  ├──Components/     # Shared React components
│  │  │  └──app.jsx         # Inertia + React entry point
│  │  └──views/            # Blade layout(s), if needed
│  ├──routes/
│  │  ├──web.php           # Web + Inertia routes
│  │  └──api.php           # Optional REST API routes (future)
│  ├──tests/               # Laravel test suite
│  ├──package.json
│  ├──vite.config.js
│  ├──composer.json
│  └──.env.example
├──coming-soon.html
├──README.md
├──CONTRIBUTING.md
├──TASKS.md
└──LICENSE.md
```

---

## Getting Started

### 1. Clone the Repository

```bash
git clone git@github.com:muliratendo/booking-umu.git
cd booking-umu/booking
```

### 2. Install Backend Dependencies

> Follow the internal docs or [CONTRIBUTING.md](./CONTRIBUTING.md) for prerequisites (Nginx, PHP-FPM, MySQL, Composer, etc.).

```bash
composer install
cp .env.example .env
php artisan key:generate
```

Configure your `.env` for MySQL:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=umu_booking
DB_USERNAME=umu_user
DB_PASSWORD=your_password_here
```

Run migrations (and seeders when available):

```bash
php artisan migrate
# php artisan db:seed
```

### 3. Install Frontend Dependencies

```bash
npm install
```

---

## Running the App

Start both servers in separate terminals:

**Terminal 1 — Laravel backend**
```bash
php artisan serve
```

**Terminal 2 — Vite dev server**
```bash
npm run dev
```

Visit the URL printed by Vite (usually `http://localhost:5173`) or the Laravel dev URL (usually `http://127.0.0.1:8000`), depending on your setup.

---

## Branching and Workflow

We follow a **feature-branch + Pull Request** workflow on top of `main`.
>**Description**: Each change is developed in its own feature branch created from the main branch, then shared by pushing that branch and opening a pull request so teammates can review, discuss, and approve the changes before merging them back into main.
### Branch Rules

| Branch | Purpose |
|---|---|
| `main` | Always deployable. Updated via Pull Requests only. |
| `feature/<area>-<short-desc>` | New features |
| `bugfix/<area>-<short-desc>` | Bug fixes |
| `docs/<short-desc>` | Documentation updates |

### Branch Examples
- `feature/academic-booking-list`
- `feature/hospital-emergency-flow`
- `bugfix/hostel-allocation-off-by-one`
- `docs/update-api-section`

### Basic Flow

```bash
git checkout main
git pull origin main
git checkout -b feature/academic-booking-list

# make your changes, then commit
git add .
git commit -m "Implement academic booking list page"

git push -u origin feature/academic-booking-list
```

Then open a **Pull Request** into `main` on GitHub, request a review, and merge only after approval.

> Full details are in [CONTRIBUTING.md](./CONTRIBUTING.md).

---

## Tasks and Team Work

All task assignments, roles, and work breakdown live in **[TASKS.md](./TASKS.md)**.

### Roles

- **Lead Devs** — Architecture, code review, and merge approvals
- **Frontend Devs** — React + Inertia pages and components
- **Backend Devs** — Laravel controllers, models, and database layer
- **QA Devs** — Performance and security testing and review
- **Design/UX Devs** — UI design, accessibility, and user flows

### Before Starting Work

1. Pick an assigned task from `TASKS.md`.
2. Follow the contribution flow in `CONTRIBUTING.md`.
3. Update the team via the WhatsApp group.

---

## Contributing Guidelines

Read **[CONTRIBUTING.md](./CONTRIBUTING.md)** before opening your first PR. It covers:

- Branch naming conventions and PR rules
- Code style for Laravel and React
- Testing expectations
- How QA and Design/UX collaborate with devs

---

## License

This project is licensed under the **MIT License**.

```
MIT License

Copyright (c) 2025 Uganda Martyrs University — Booking@UMU Platform Contributors

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

See the [LICENSE](./LICENSE) file in the repository root for the full license text. 

---