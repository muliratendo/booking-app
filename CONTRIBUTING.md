# Contributing to Booking@UMU

[🏠 README](./README.md) &nbsp;&nbsp;•&nbsp;&nbsp; [🤝 THIS FILE](./CONTRIBUTING.md) &nbsp;&nbsp;•&nbsp;&nbsp; [📋 TASKS](./docs/TASKS.md)

Thank you for your interest in contributing to **Booking@UMU**. This document explains how we work together, how to run the project locally, and how to troubleshoot common issues.

- [How We Work](#how-we-work)
- [Roles and Responsibilities](#roles-and-responsibilities)
- [Branching and Git Workflow](#branching-and-git-workflow)
- [Coding Standards](#coding-standards)
- [How to Run Booking@UMU Locally](#how-to-run-bookingumu-locally)
  - [Windows (WSL2 + Ubuntu + Nginx)](#windows-wsl2--ubuntu--nginx)
  - [macOS (Homebrew + Nginx)](#macos-homebrew--nginx)
  - [Linux (Ubuntu + Nginx)](#linux-ubuntu--nginx)
- [Common Errors and How to Fix Them](#common-errors-and-how-to-fix-them)
- [How to Submit Changes](#how-to-submit-changes)
- [Where to Get Help](#where-to-get-help)

---

## How We Work

Booking@UMU is a multi-module Laravel + React (Inertia) platform for academic booking, hostel allocation, and hospital services. All work happens in a **single GitHub repository** using:

- Laravel for the backend.
- React + Inertia for the SPA-style frontend.
- MySQL as the main database.
- Nginx as the web server (with HTTPS in local dev).
- Git + GitHub for collaboration.

We use feature branches and Pull Requests (PRs) on top of a protected `main` branch.

For a high-level project overview and setup, see **[README.md](./README.md)**.  
For the current work breakdown and assignments, see **[TASKS.md](./TASKS.md)**.

---

## Roles and Responsibilities

The team is organized into:

### Lead Developers

- Define architecture and folder structure.
- Review critical PRs (security, migrations, core modules).
- Approve releases and tag versions.
- Mentor other contributors.

### Frontend Developers (React + Inertia)

- Work primarily in `resources/js/Pages`, `resources/js/Components`, and styling.
- Implement React pages for:
  - Phase 1: Hostel Allocation
  - Phase 2: Academic Booking
  - Phase 3: Hospital Services
- Ensure good UX and responsive layouts.
- Coordinate with Design/UX devs.

### Backend Developers (Laravel)

- Work primarily in `app/`, `routes/`, `database/`, and `config/`.
- Implement models, controllers, policies, jobs, and migrations.
- Expose data to React via Inertia responses.
- Maintain database integrity and transactional logic.

### Quality Assurance Developers

- Write and maintain automated tests:
  - `tests/Feature` and `tests/Unit` for Laravel.
  - JS tests later (e.g. Jest/React Testing Library) under `resources/js/__tests__/`.
- Create manual test plans for new features.
- Verify bug fixes and regressions before merging/ releasing.

> QA engineers are expected to perform basic security testing following PortSwigger Web Security Academy guidance, including checks for input validation issues (XSS, SQL injection), authentication and access control flaws, CSRF, file upload issues, and common misconfigurations before approving changes.

### Design and UX Developers

- Own Figma / design system for Booking@UMU.
- Provide UI specs, components, and UX flows.
- Review implementations and open design-related issues.
- May contribute directly to React components and CSS.
  > **NB:** Contact the Led Devs for access to our Figma for Eduaction Team Space.

---

## Branching and Git Workflow

We use **feature-branch + PRs on top of `main`** (GitHub Flow).

### Protected `main`

- Always deployable.
- Updated **only** via Pull Requests (no direct pushes).

### Branch Naming

All work starts from `main`:

```bash
git checkout main
git pull origin main
git checkout -b <branch-name>
```

Branch types:

- Features:  
  `feature/<area>-<short-desc>`

- Bug fixes:  
  `bugfix/<area>-<short-desc>`

- Documentation:  
  `docs/<short-desc>`

**Examples**

- `feature/academic-booking-list`
- `feature/hospital-emergency-flow`
- `bugfix/hostel-allocation-off-by-one`
- `docs/update-api-section`

### Basic Workflow

1. Pull latest `main`.
2. Create a feature/bugfix/docs branch.
3. Commit changes in small, logical chunks.
4. Push branch to GitHub.
5. Open a Pull Request into `main`.
6. Request review (at least one lead dev approval).
7. Address feedback and merge via GitHub.

---

## Coding Standards

### PHP / Laravel

- Follow PSR-12 where applicable.
- Keep controllers thin: use Form Requests, Services, and Resources.
- Prefer Eloquent relationships and query scopes.
- Use migrations and seeders for DB changes; never modify DB manually in production.

### JavaScript / React

- Use modern ES syntax and React functional components with hooks.
- Prefer composition over deeply nested components.
- Keep components small and focused.
- Use a shared components library under `resources/js/Components` for buttons, layouts, tables, etc.

### General

- Write meaningful commit messages:
  - `Add hostel allocation table`
  - `Fix null pointer in emergency escalation handler`
- Add or update tests when you change behavior.
- Keep PRs focused on one logical change.

---

## GitHub SSH Setup (Git ⇄ GitHub)

All approved contributors should use SSH keys to interact with GitHub (clone, push, pull) instead of HTTPS passwords.

1.  Generate an SSH key pair
    On your development machine (Linux/WSL/macOS/Windows):

    ```bash
     ssh-keygen -t ed25519 -C "your-email@example.com"
    ```

        When prompted for file path, you can accept the default:

    `~/.ssh/id_ed25519`

    Optionally set a passphrase for extra security. This creates:

    Private key: `~/.ssh/id_ed25519 `

    Public key: `~/.ssh/id_ed25519.pub `

2.  Add SSH key to ssh-agent (recommended)
    Start the agent and add your key:

    ```bash
     eval "$(ssh-agent -s)"
     ssh-add ~/.ssh/id_ed25519
    ```

3.  Add your SSH key to GitHub
    Show your public key:

    ```bash
     cat ~/.ssh/id_ed25519.pub
     Copy the entire output.
    ```

        Go to GitHub:
        - User Settings* → SSH and GPG keys → New SSH key
        - Title: something like  `Booking@UMU Dev Laptop `
        - Paste the public key.
        - Save.

          ***\*Click on your GitHub acc icon to access user settings***

4.  Test the connection

    ```bash
      ssh -T git@github.com
    ```

    You should see a message like:

    ```text
         Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
    ```

5.  Clone using SSH

    From now on, always clone using the SSH URL. Continue below to clone the repo.

    ```bash
    git clone git@github.com:muliratendo/booking-umu.git
    ```

    ```bash
    cd booking-umu/booking
    git switch <personal-branch-name>
    ```

6.  Install Laravel Extension

    In your IDE(VsCode, Antigravity, etc), Select the Extensions tab in the left sidebar and search for Laravel Extension. Install it.

---

## How to Run Booking@UMU Locally

Below is a **step-by-step** guide for running Booking@UMU locally with Nginx on Windows (via WSL2), macOS, or Linux.

> **Note:** You do not need to run Booking@UMU locally to contribute to the project. You can use a regular code editor + Git + GitHub to submit pull requests.

### Common prerequisites

You will need:

- PHP 8.x + PHP extensions (`php-fpm`, `php-mbstring`, `php-xml`, `php-mysql`, `php-curl`, `php-zip`, `php-bcmath`).
- Composer
- Node.js + npm
- MySQL (or MariaDB)
- Nginx
- `mkcert` (for local HTTPS certificates)

> Note: The exact commands differ per OS, but the structure is the same: install stack → configure database → configure Nginx → run Laravel and Vite.

---

_After Installing the prerequisites depending on your OS(refer to AI or Google Search) continue a show below:_

- ### [Windows Users](#windows-wsl2--ubuntu--nginx)
- ### [Mac Users](#macos--homebrew--nginx)
- ### [Linux Users](#linux--ubuntu--nginx)

---

### Windows (WSL2 + Ubuntu + Nginx)

1.  **Install and Open WSL2 Ubuntu**
    Open Windows Terminal 'Win' Key, then search 'Terminal'. Right click on the app and select 'Run as Administrator'

    Run:

    ```powershell
        wsl --install
        wsl
    ```
    **Restart your computer after installation.**

2.  Install Composer, Node, mkcert
    Install composer and node using your preferred method, then:

        ```bash
          sudo apt install -y mkcert libnss3-tools
          mkcert -install
        ```

3.  **Clone Booking@UMU**

    > [Setup GitHub SSH before proceeding](github-ssh-setup-git-github)

    ```bash
      cd /var/www
      sudo mkdir -p booking-umu
      sudo chown -R $USER:$USER booking-umu
      cd booking-umu

      git clone git@github.com:muliratendo/booking-umu.git

    ```

4.  **Install backend and frontend dependencies**

    ```bash
    composer install
    cp .env.example .env
    php artisan key:generate

    npm install
    ```

    Create MySQL database

    ```bash
    sudo mysql

    CREATE DATABASE booking_umu CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
    CREATE USER 'booking_admin'@'localhost' IDENTIFIED BY 'strong_password_here';
    GRANT ALL PRIVILEGES ON booking_umu.* TO 'booking_admin'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```

    \*Replace '_strong_password_here_' with a strong password including;

    ```text
     - Capital letters
     - Small letters
     - Numbers
     - Symbols
    ```

    Update `.env`:

    ```env
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=3306
    DB_DATABASE=booking_umu
    DB_USERNAME=booking_admin
    DB_PASSWORD=strong_password_here
    ```

    \*Replace '_strong_password_here_' with the strong password you created.

    Run migrations:

    ```bash
    php artisan migrate
    ```

5.  **Generate local HTTPS cert for booking.umu**

    From your project or a certs directory:

    ```bash
      mkcert booking.umu
    ```

    This creates e.g.:
    - `booking.umu.pem`
    - `booking.umu-key.pem`

    Move them to a stable path

    ```bash
      sudo mkdir -p /etc/ssl/booking-umu
      sudo mv booking.umu.pem /etc/ssl/booking-umu/
      sudo mv booking.umu-key.pem /etc/ssl/booking-umu/
    ```

    Adjust ownership permissions as needed:

    ```bash
      sudo chown root:root /etc/ssl/booking-umu/booking.umu.pem /etc/ssl/booking-umu/booking.umu-key.pem
      sudo chmod 600 /etc/ssl/booking-umu/booking.umu-key.pem
      sudo chmod 644 /etc/ssl/booking-umu/booking.umu.pem
    ```

6.  **Configure Nginx (WSL)**

    Create a site config:

    ```bash
    sudo nano /etc/nginx/sites-available/booking-umu.conf
    ```

    \*Press 'ALT + M' to toggle mouse support _on_ or _off_

    Paste:

    ```nginx
    server {
             listen 80;
             listen [::]:80;
             server_name booking.umu;
             return 301 https://$host$request_uri;
     }

     server {
             listen 443 ssl;
             listen [::]:443 ssl;
             server_name booking.umu;

             ssl_certificate     /etc/ssl/booking-umu/booking.umu.pem;
             ssl_certificate_key /etc/ssl/booking-umu/booking.umu-key.pem;

             root /var/www/booking-umu/public;
             index index.php index.html;

             add_header X-Frame-Options "SAMEORIGIN";
             add_header X-Content-Type-Options "nosniff";

             location / {
                 try_files $uri $uri/ /index.php?$query_string;
             }

             location ~ \.php$ {
                 include snippets/fastcgi-php.conf;
                 fastcgi_pass unix:/var/run/php/php-fpm.sock;
             }

             location ~ /\.ht {
                 deny all;
             }
     }

    ```

    \*To save and exit _Nano Editor_, press 'CTRL + X' then 'y' then 'ENTER' Key to save and exit nano.

    Enable site and reload Nginx:

    ```bash
    sudo ln -s /etc/nginx/sites-available/booking-umu.conf /etc/nginx/sites-enabled/
    sudo rm /etc/nginx/sites-enabled/default 2>/dev/null || true
    sudo nginx -t
    sudo service nginx reload
    ```

7.  **Map host name in Windows**

    Edit `C:\Windows\System32\drivers\etc\hosts` in Windows using VS Code:

    ```text
    ::1 booking.umu
    127.0.0.1 booking.umu
    ::1 www.booking.umu
    127.0.0.1 www.booking.umu
    ```

    ...and save as Administrator.

8.  **Run Vite dev server**

    In WSL:

    ```bash
    npm run dev
    ```

9.  **Access Booking@UMU**

    Visit:
    - `booking.umu/` in your Windows or Chrome browser. (it should show as a trusted HTTPS site).

---

### MacOS (Homebrew + Nginx)

1. **Install dependencies with Homebrew**

   ```bash
     brew install nginx mysql php composer node
   ```

2. **Clone Booking@UMU**

   ```bash
     cd ~/Sites
     mkdir -p booking-umu
     cd booking-umu
     git clone git@github.com:muliratendo/booking-app.git
   ```

3. **Install PHP/JS dependencies and configure `.env`**

   Same as WSL:

   ```bash
     composer install
     cp .env.example .env
     php artisan key:generate

     npm install
   ```

   Create DB in MySQL and update `.env` as shown in the [Windows Users](#Windows "WSL2 + Ubuntu + Nginx") Section 4 above, then:

   ```bash
   php artisan migrate
   ```

4. **Generate cert and configure Nginx on macOS**

   From a convenient directory (e.g. your project root):

   ```bash
   cd ~/Sites/booking-umu   # or your actual project path
   mkcert booking.umu
   ```

   This will create files like:
   - `booking.umu.pem – the certificate`
   - `booking.umu-key.pem – the private key`

   Create a dedicated directory for Nginx SSL certs and move the files:

   ```bash
     sudo mkdir -p /opt/homebrew/etc/nginx/certs
     sudo mv booking.umu.pem /opt/homebrew/etc/nginx/certs/
     sudo mv booking.umu-key.pem /opt/homebrew/etc/nginx/certs/
   ```

   Adjust ownership/permissions:

   ```bash
     sudo chown root:wheel /opt/homebrew/etc/nginx/certs/booking.umu*
     sudo chmod 600 /opt/homebrew/etc/nginx/certs/booking.umu-key.pem
     sudo chmod 644 /opt/homebrew/etc/nginx/certs/booking.umu.pem
   ```

   Create booking-umu.conf for nginx:

   ```bash
   sudo nano /opt/homebrew/etc/nginx/servers/booking-umu.conf
   ```

   \*Press 'Option + M' to toggle mouse support _on_ or _off_

   Paste:

   ```nginx
   server {
            listen 80;
            server_name booking.umu;
            return 301 https://$host$request_uri;
   }

   server {
            listen 443 ssl;
            server_name booking.umu;

            ssl_certificate     /opt/homebrew/etc/nginx/certs/booking.umu.pem;
            ssl_certificate_key /opt/homebrew/etc/nginx/certs/booking.umu-key.pem;

            root /Users/<your-username>/Sites/booking-umu/public;
            index index.php index.html index.htm;

            location / {
                try_files $uri $uri/ /index.php?$query_string;
   }

            location ~ \.php$ {
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                include        fastcgi_params;
                fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
            }

            location ~ /\.ht {
               deny all;
            }
   }
   ```

   \*On line 14 above, replace '<your-mac-username\>' with your actual username. From Terminal, run `whoami` to determine your username.

   \*To save and exit _Nano Editor_, press 'CTRL + X' then 'y' then 'ENTER' Key to save and exit nano.

   Make sure `nginx.conf` includes the `servers` directory:

   From Terminal, run

   ```bash
     sudo nano /opt/homebrew/etc/nginx/nginx.conf
   ```

   or

   ```bash
     sudo nano /usr/local/etc/nginx/nginx.conf
   ```

   Find the http { ... } block

   You’ll see something like:

   ```text
     http {
         include       mime.types;
         default_type  application/octet-stream;

         # ... other config ...
     }
   ```

   Add the servers include inside that `http { ... }` block (not outside), add:

   ```text
       include /opt/homebrew/etc/nginx/servers/*.conf;
   ```

   Full example:

   ```text
     http {
           include       mime.types;
           default_type  application/octet-stream;

           # existing settings...

           include /opt/homebrew/etc/nginx/servers/*.conf;
     }
   ```

   Save and exit ('Ctrl+O', 'Enter' key, 'Ctrl+X' in nano editor).

   Restart Nginx:

   ```bash
     nginx -t
     brew services restart nginx
   ```

5. **Map host name**

   Open the file:

   ```bash
     sudo nano /etc/hosts
   ```

   \*Press 'Option + M' to toggle mouse support _on_ or _off_

   Move the cursor to the bottom (or wherever you want) and add this line:

   ```text
     127.0.0.1   booking.umu
   ```

   Make sure there’s at least one space or tab between 127.0.0.1 and booking.umu.

   \*To save and exit _Nano Editor_, press 'CTRL + X' then 'y' then 'ENTER' Key to save and exit nano.

   Flush DNS cache so changes apply immediately:

   ```bash
     sudo dscacheutil -flushcache
     sudo killall -HUP mDNSResponder
   ```

6. **Run Vite dev server**

   ```bash
   npm run dev
   ```

7. **Access Booking@UMU**

   Visit `booking.umu/` to view the website.

---

### Linux (Ubuntu + Nginx)

Steps are similar to WSL, but using Linux terminal without installing wsl:

1. Install Nginx, PHP, MySQL, Node.
2. Clone the repo into `/var/www/booking-app`.
3. Set correct permissions:

   ```bash
   sudo chown -R www-data:www-data /var/www/booking-app
   sudo chmod -R 775 /var/www/booking-umu/storage /var/www/booking-app/bootstrap/cache
   ```

4. Create DB and update `.env`, then migrate.
5. Configure Nginx with `root /var/www/booking-umu/public;`.
6. Restart Nginx:

   ```bash
   sudo systemctl restart nginx
   ```

7. Run `npm run dev` and access via your configured domain or `http://localhost`.

---

## Common Errors and How to Fix Them

### 1. `could not find driver (Connection: sqlite...)`

Cause: Laravel is trying to use SQLite but you either don’t have the SQLite driver installed or you actually want MySQL.

Fix:

- Use MySQL in `.env` as shown above, or install `php-sqlite3` if you really want SQLite.

### 2. 502 Bad Gateway (Nginx + PHP-FPM)

Common reasons:

- PHP-FPM not running.
- Wrong `fastcgi_pass` socket/port.
- Permissions on Laravel folders.[web:331][web:333]

Checklist:

- Ensure PHP-FPM service is running (name may vary):

  Windows + Linux

  ```bash
  sudo service php8.2-fpm status   # example
  ```

  Mac

  ```bash
  php -v
  brew list | grep php
  ```

- Match `fastcgi_pass` in Nginx to the actual socket or port (e.g. `unix:/var/run/php/php-fpm.sock` or `127.0.0.1:9000`).

- Fix file permissions:

  ```bash
  sudo chown -R www-data:www-data /var/www/booking-umu
  sudo chmod -R 775 /var/www/booking-umu/storage /var/www/booking-umu/bootstrap/cache
  ```

- Check Laravel logs:

  ```bash
  tail -f storage/logs/laravel.log
  ```

### 3. 404 on all routes (Nginx)

Cause: Wrong `root` or missing `try_files` rule.

Fix:

- Ensure Nginx `root` points to Laravel’s **public** directory, not project root:

  ```nginx
  root /var/www/booking-umu/public;
  ```

- Ensure `location /` has:

  ```nginx
  try_files $uri $uri/ /index.php?$query_string;
  ```

[web:330][web:335]

### 4. `php artisan` commands failing

Common issues:

- Missing `.env` file.
- Wrong DB config.
- Missing PHP extensions.

Fix:

- Ensure `.env` exists and `APP_KEY` is set (`php artisan key:generate`).
- Check DB section in `.env`.
- Install missing extensions (`php-mbstring`, `php-xml`, `php-curl`, etc.).

### 5. Node/Vite issues

- If `npm run dev` fails:
  - Ensure correct Node version.
  - Delete `node_modules` and reinstall:

    ```bash
    rm -rf node_modules
    npm install
    ```

---

## How to Submit Changes

1. Pick or be assigned a task from `TASKS.md`.
2. Create a branch from `main`:

   ```bash
   git checkout main
   git pull origin main
   git checkout -b feature/<area>-<short-desc>
   ```

3. Make changes and add tests where appropriate.
4. Run tests:

   ```bash
   php artisan test
   # JS tests later, e.g. npm test
   ```

5. Commit and push:

   ```bash
   git add .
   git commit -m "Short description of change"
   git push -u origin feature/<area>-<short-desc>
   ```

6. Open a Pull Request into `main`:
   - Fill out the PR template.
   - Include screenshots for UI changes.
   - Tag relevant reviewers (lead devs, QA, Design).

7. Respond to review feedback, then merge after approval.

---

## Where to Get Help

If you’re stuck:

- Check:
  - `README.md`
  - `TASKS.md`
  - This `CONTRIBUTING.md`
- Ask in the team’s agreed communication channel (e.g., WhatsApp/Slack/Teams) with:
  - OS (Windows/WSL, macOS, Linux)
  - Exact command you ran
  - Exact error message
  - Relevant log output (e.g., Nginx error log, `storage/logs/laravel.log`)
- Alternatively, ask AI, and verify responses.

> We’re building Booking@UMU as a learning project and a real system; clear questions and detailed issues help everyone move faster.