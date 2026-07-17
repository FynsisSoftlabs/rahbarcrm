# RahbarCRM

**Open-source CRM reimagined with AI**

RahbarCRM is a self-hosted CRM re-engineered with AI at its core — built for teams who want full control over their data without giving up modern, intelligent tooling.

---

## Overview

RahbarCRM combines a traditional CRM's core workflow (contacts, accounts, leads, opportunities, activities) with AI-powered features that reduce manual work: intelligent email handling, automated workflows, a knowledge assistant you can reach over WhatsApp, and built-in calendar & scheduling.

Because it's self-hosted, your data stays on infrastructure you control — RahbarCRM doesn't require sending your customer data to a third-party SaaS platform.

## Features

- **Email Intelligence** — AI-assisted email handling, summarization, and triage inside your CRM workflow.
- **Workflow Automation** — automate repetitive CRM processes so your team spends less time on manual data entry.
- **WhatsApp / RAG Knowledge Assistant** — a retrieval-augmented assistant your team can query for CRM data and knowledge, accessible via WhatsApp.
- **Calendar & Scheduling** — built-in scheduling tied directly into your CRM records.
- **Self-hosted** — deploy on your own infrastructure, on your own terms.

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | PHP 8.2 / 8.3 / 8.4 |
| Web Server | Apache 2.4 |
| Database | MariaDB 10.6+ or MySQL 8.0+ |
| Front-end (build tooling) | Angular CLI, Node.js, yarn *(development only — not required for pre-built installs)* |

See [`install.md`](./install.md) for the full list of required PHP modules and supported browsers.

## Getting Started

### Prerequisites

- A server running Linux, Unix, or Mac OS
- PHP 8.2, 8.3, or 8.4 with the required extensions
- Apache 2.4 with `mod_rewrite` enabled
- MariaDB or MySQL

Full prerequisite details are in [`install.md`](./install.md).

## Installation

### Option 1: Install from GitHub Release (Recommended)

1. Download the latest pre-built installable package from the **Releases** page.
2. Upload the package to your web server's document root.
3. Extract the package and set the required file permissions.
4. Run the installer using either the browser-based installer or the CLI installer.
5. Configure the required cron job and messenger worker for scheduled and background tasks.

### Option 2: Install Directly from GitHub

1. Clone the repository:

```bash
git clone https://github.com/fynsis/rahbarcrm.git
```

2. Navigate to the project directory:

```bash
cd rahbarcrm
```

3. Install PHP dependencies:

```bash
composer install --no-dev --optimize-autoloader
```

4. Create the environment configuration:

```bash
cp .env.example .env
```

5. Open the `.env` file and configure your database credentials and application settings.

6. Set the required file and directory permissions.

7. Run the installer using either the browser-based installer or the CLI installer.

8. Configure the required cron job and messenger worker for scheduled and background tasks.

Full step-by-step instructions, including both installation methods and post-install configuration, are available in **install.md**.

### Option 3: Download on your local and install

1. Download the pre-built installable package for your release.
2. Copy it to your webserver's web root and set file permissions.
3. Run the installer — via the browser-based UI installer or the CLI installer.
4. Configure the required cron job and messenger worker for scheduled and background tasks.

Full step-by-step instructions, including both installer options and post-install configuration, are in [`install.md`](./install.md).

### Quick Start (CLI Installer)

```bash
./bin/console suitecrm:app:install -u "admin" -p "your_admin_password" -U "db_user" -P "db_password" -H "db_host" -N "rahbarcrm" -S "https://yourcrm.com/" -d "no"
```

See [`install.md`](./install.md) for the full parameter reference.

## Documentation

- [Installation Guide](./install.md) — full setup instructions, prerequisites, and troubleshooting
- [License](./LICENSE.md) — license terms

## License

RahbarCRM is licensed under RahbarCRM's own license, adapted from the Business Source License 1.1 framework. In short:

- ✅ Free to use in production, for anyone, at any scale
- ✅ Free to modify and extend for your own use
- ❌ May not be cloned, forked, or re-implemented and passed off as an independent product
- ❌ May not be resold or offered as a hosted service under a different name or brand without written authorization

See [`LICENSE.md`](./LICENSE.md) for the full terms.

## Support & Contact

**RahbarCRM — Powered by Fynsis Softlabs**

- Website: [rahbarcrm.com](https://rahbarcrm.com)
- Support: [support@fynsis.com](mailto:support@fynsis.com)
- Licensing & Partnerships: [sales@fynsis.com](mailto:sales@fynsis.com)

---

*Copyright © 2026 Fynsis Softlabs. All rights reserved.*
