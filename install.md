# RahbarCRM Installation Guide

*A complete guide to downloading, configuring, and installing RahbarCRM*

**Version:** 1.1 | **Date:** July 17, 2026 | **Prepared for:** End Users / Self-Hosters

---

## Table of Contents

1. [Overview & Purpose](#1-overview--purpose)
2. [Audience](#2-audience)
3. [Prerequisites](#3-prerequisites)
4. [System Requirements: Webserver Setup](#4-system-requirements-webserver-setup)
5. [Downloading and Setting Up RahbarCRM](#5-downloading-and-setting-up-rahbarcrm)
   - [5.1 Option 1: Pre-Built Installable Package](#51-option-1-pre-built-installable-package)
   - [5.2 Option 2: Installing from Source via GitHub](#52-option-2-installing-from-source-via-github)
6. [Installation Steps](#6-installation-steps)
   - [6.1 Option A — UI Installer](#61-option-a--ui-installer)
   - [6.2 Option B — CLI Installer](#62-option-b--cli-installer)
7. [Configuration](#7-configuration)
8. [Post-Install Verification](#8-post-install-verification)
9. [Troubleshooting](#9-troubleshooting)
10. [Support Contact](#10-support-contact)

---

## 1. Overview & Purpose

This guide walks you through installing RahbarCRM on your own server, from the moment you provision infrastructure through to logging into your first working instance. It is written for end users and self-hosters who are setting up RahbarCRM independently, without relying on a managed hosting provider.

RahbarCRM is an open-source, self-hosted CRM re-engineered with AI — including email intelligence, workflow automation, a WhatsApp/RAG knowledge assistant, and calendar & scheduling. Because it is self-hosted, you have full control over where your data lives and how your instance is configured, but that also means the installation and environment setup described below is your responsibility to complete correctly.

---

## 2. Audience

This guide assumes you are comfortable with:

- Basic Linux/Unix server administration (navigating a filesystem, editing config files, using a terminal).
- Running shell commands, including file permission changes.
- Basic webserver and database concepts (virtual hosts, database users, ports).
- If installing from source: basic familiarity with `git`, PHP dependency management (Composer), and Node-based front-end builds.

No prior experience with RahbarCRM itself is required — every step below is self-contained.

---

## 3. Prerequisites

Before you begin, provision a server (or local environment) that meets the following requirements.

### 3.1 Platform

| Requirement | Supported Versions |
|---|---|
| Operating System | Linux, Unix, or Mac OS (any version that supports PHP) |
| PHP | 8.2, 8.3, 8.4 |
| Web Server | Apache 2.4 |
| Database | MariaDB 10.6, 10.11, 11.4, 11.8 &nbsp;**or**&nbsp; MySQL 8.0, 8.4 |

### 3.2 Supported Browsers

| Browser | Minimum Version |
|---|---|
| Chrome | 143+ |
| Firefox | 140, 146+ |
| Edge | 143+ |
| Safari | 26+ |

### 3.3 Front-End Build Tools

Required only if you are installing **from source via GitHub** (see [Section 5.2](#52-option-2-installing-from-source-via-github)). Not required when installing the pre-built package.

| Tool | Version |
|---|---|
| Angular CLI | ^18 |
| Node.js | ^20.11.1 |
| yarn | ^4.10.3 |
| Composer | 2.x |
| Git | Any recent version |

### 3.4 Required PHP Modules

- cli
- curl
- common
- intl
- json
- gd
- mbstring
- mysqli
- pdo_mysql
- openssl
- soap
- xml
- zip
- imap *(optional)*
- ldap *(optional)*

---

## 4. System Requirements: Webserver Setup

This guide uses a LAMP stack (Linux, Apache, MySQL/MariaDB, PHP) as the reference setup.

### 4.1 Install Required Software

- A webserver — **Apache**
- **PHP** (see version table above)
- A database — **MySQL** or **MariaDB**

> When installing from the pre-built installable package, you do **not** need Node.js, Angular CLI, yarn, Composer, or Git — the package already includes compiled front-end assets and vendor libraries. These are only needed for the GitHub source install path in [Section 5.2](#52-option-2-installing-from-source-via-github).

### 4.2 Configure URL Rewrites

API routes depend on URL rewriting. Without it, API calls (such as GraphQL requests) return a `404` error. Enable `mod_rewrite` for Apache.

**Point your virtual host at the application's `public` folder, not the application root** — otherwise internal application files may be exposed to the web.

```xml
<VirtualHost *:80>

    DocumentRoot /<path-to-app>/public
    <Directory /<path-to-app>/public>
        AllowOverride All
        Order Allow,Deny
        Allow from All
    </Directory>

</VirtualHost>
```

### 4.3 Configure PHP Error Reporting

Update `error_reporting` in `php.ini` so notices are not surfaced:

```ini
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT & ~E_NOTICE & ~E_WARNING
```

---

## 5. Downloading and Setting Up RahbarCRM

You can set up RahbarCRM in one of two ways: installing the **pre-built package** (simplest, recommended for most self-hosters) or **building from source via GitHub** (for development environments, custom builds, or contributors).

### 5.1 Option 1: Pre-Built Installable Package

#### 5.1.1 Download the Installable Package

Download the pre-built installable package for your target release.

#### 5.1.2 Copy Files to the Webserver Root

1. Unzip the pre-built installable package.
2. Copy the extracted files to your webserver's web root (for Apache, typically `/var/www` or `/var/www/html` — make sure this matches the `DocumentRoot` from [Section 4.2](#42-configure-url-rewrites)).

Skip ahead to [Section 5.3](#53-set-file-permissions) once files are in place.

### 5.2 Option 2: Installing from Source via GitHub

Use this path if you want to build RahbarCRM yourself — for example, to track the latest development branch, apply custom patches, or contribute back to the project.

#### 5.2.1 Clone the Repository

```bash
git clone https://github.com/FynsisSoftlabs/rahbarcrm.git
cd rahbarcrm
```

To install a specific tagged release instead of the latest commit on the default branch, check out the corresponding tag after cloning:

```bash
git tag -l                 # list available release tags
git checkout tags/<version-tag>
```

#### 5.2.2 Install PHP Dependencies

From the project root, install backend dependencies with Composer:

```bash
composer install --no-dev --optimize-autoloader
```

Omit `--no-dev` if you are setting up a development environment and want dev-only tooling installed as well.

#### 5.2.3 Install Front-End Dependencies and Build

Install Node dependencies with yarn and produce a production build of the front end:

```bash
yarn install
yarn build
```

This compiles the Angular front end into the application's `public` directory. If your checkout uses a different build script name, check the `scripts` section of `package.json` in the repository, or the project's `CONTRIBUTING.md`, for the exact command.

#### 5.2.4 Move Files to the Webserver Root

Copy (or symlink) the cloned and built project directory into your webserver's web root, matching the `DocumentRoot` configured in [Section 4.2](#42-configure-url-rewrites).

```bash
cp -r rahbarcrm/. /var/www/html/rahbarcrm/
```

> **Note:** Commands in this section reflect the standard PHP/Composer + Node/yarn build pattern used by this stack. If the repository's own README or CONTRIBUTING guide specifies different build steps, follow those instead — they take precedence over this guide for anything specific to a given release.

### 5.3 Set File Permissions

From the terminal, run (regardless of which installation option you used):

```bash
find . -type d -not -perm 2755 -exec chmod 2755 {} \;
find . -type f -not -perm 0644 -exec chmod 0644 {} \;
find . ! -user www-data -exec chown www-data:www-data {} \;
chmod +x bin/console
```

Replace `www-data` with the actual user/group your webserver runs under (e.g. `www-data` on Ubuntu/Apache, `apache` on some other Linux distributions). If your webserver's group differs from its username, use `0664` instead of `0644`, and `2775` instead of `2755`.

### 5.4 Create the Database

Depending on your setup, you may need to create the target database yourself before installing. The installer creates the required tables inside it during installation.

---

## 6. Installation Steps

You can install RahbarCRM using either the UI installer (browser-based) or the CLI installer (command line). This applies the same way whether you installed the pre-built package or built from source.

### 6.1 Option A — UI Installer

**Step 1: Pre-Install Check**
Navigate to your instance's URL in a browser. Each requirement check shows **OK** or an error/warning with details. Resolve any issues, then click **RECHECK**.

**Step 2: Proceed to Install**
Once all required checks pass, click **Proceed** to reach the install page at:

```
https://<your-instance-path>/#/install
```

**Step 3: Basic System Configuration**

| Field | Description |
|---|---|
| URL of instance | The URL where your instance is located, e.g. `https://example-domain.com` |
| Database User | Username with permission to create and write to the database |
| Database User Password | Password for the database user |
| Host Name | Database host; use an IP (e.g. `127.0.0.1`) if `localhost` causes socket-connection issues |
| Database Name | Name of the database to create/use during install |
| Database Port | Defaults to the standard port; change only for non-default setups |
| Populate with Demo Data | Whether to pre-populate the database with sample data |
| Admin Username | Username for the administrator account (defaults to `admin`) |
| Admin Password | Password for the administrator account |

**Step 4: Ignore Warnings (Optional)**
Non-critical checks (e.g. max upload file size, memory limit) can be bypassed by checking **"Ignore System Check Warnings."**

**Step 5: Run the Install**
After accepting the license and completing configuration, click **Proceed**. The system re-validates requirements, then runs the install (this can take a few minutes) and redirects you to the login page.

**Step 6: Double-Check Configuration**

- `public/legacy/config.php` — `site_url`: if your virtual host does not point to the `public` directory, append `/public` to the host value.
- `public/legacy/.htaccess` — `RewriteBase`: `/legacy` if the vhost points to `public`; otherwise the full path up to the `public` folder.

**Step 7: Access the Application**
Log in using the administrator credentials configured above.

### 6.2 Option B — CLI Installer

**Step 1: Run the Install Command**

Interactive (prompts for each value):

```bash
./bin/console suitecrm:app:install
```

One-line (all options passed directly):

```bash
./bin/console suitecrm:app:install -u "admin_username" -p "admin_password" -U "db_user" -P "db_password" -H "db_host" -N "db_name" -S "site_url" -d "demo_data"
```

Example:

```bash
./bin/console suitecrm:app:install -u "admin" -p "pass" -U "root" -P "dbpass" -H "mariadb" -N "rahbarcrm" -S "https://yourcrm.com/" -d "yes"
```

To view all supported arguments:

```bash
./bin/console suitecrm:app:install --help
```

| Flag | Parameter | Description |
|---|---|---|
| `-S` | `site_url` | URL where the instance is located |
| `-U` | `db_user` | Database username with create/write permission |
| `-P` | `db_password` | Password for the database user |
| `-H` | `db_host` | Database host (use an IP if `localhost` causes socket issues) |
| `-N` | `db_name` | Database name to create/use |
| — | `db_port` | Defaults to the standard port |
| `-d` | `demo_data` | `yes` / `no` — pre-populate with demo data |
| `-u` | `admin_username` | Administrator account username |
| `-p` | `admin_password` | Administrator account password |
| — | `sys_check_option` | `true` / `false` — ignore non-critical warnings |

**Step 2: Re-Set Permissions**

```bash
find . -type d -not -perm 2755 -exec chmod 2755 {} \;
find . -type f -not -perm 0644 -exec chmod 0644 {} \;
find . ! -user www-data -exec chown www-data:www-data {} \;
chmod +x bin/console
```

**Step 3: Double-Check Configuration**

- `public/legacy/config` — `site_url`: append `/public` if the vhost doesn't point to the `public` directory.
- `public/legacy/.htaccess` — `RewriteBase`: `/legacy` if vhost points to `public`; otherwise full path up to `public`.

**Step 4: Access the Application**
Your instance should now be reachable at `https://<your-instance-path>`.

---

## 7. Configuration

| Setting | Location | Description |
|---|---|---|
| `site_url` | `public/legacy/config.php` | Public URL of the instance; must include `/public` if the vhost doesn't already point there |
| `RewriteBase` | `public/legacy/.htaccess` | Path prefix used by URL rewriting; must match your vhost path |
| `DocumentRoot` | Apache vhost config | Should point to the app's `public` folder, not the app root |

---

## 8. Post-Install Verification

Two background processes must be configured after installation completes — the application will not fully function without them.

### 8.1 Cron Job (Scheduled Tasks)

Required for workflows, email checks, report generation, and other scheduled tasks. Configure a system cron entry that triggers the application's scheduler process (check your Administration Panel's Schedulers section for the exact command and recommended interval — typically every minute). Without this, scheduled tasks never run.

### 8.2 Messenger Worker (Async Tasks)

Background tasks (e.g. manual migration tasks) are processed by a queue worker. Without a persistently running worker (managed via `systemd`, `supervisor`, or similar), these tasks remain stuck in **"Pending"** status indefinitely.

### 8.3 Verification Checklist

- [ ] Server meets PHP, webserver, and database version requirements
- [ ] Required PHP modules installed
- [ ] Apache `mod_rewrite` enabled; vhost points to the `public` directory
- [ ] PHP `error_reporting` configured
- [ ] Package downloaded (or repository cloned and built) and placed in the webroot
- [ ] File permissions and ownership set correctly
- [ ] Database created (if required)
- [ ] Installer completed (UI or CLI) with correct database and site URL values
- [ ] `config.php` / `.htaccess` `RewriteBase` double-checked post-install
- [ ] Cron job configured for scheduled tasks
- [ ] Messenger worker configured and running
- [ ] Able to log in with the administrator account

---

## 9. Troubleshooting

| Symptom | Likely Cause | Fix |
|---|---|---|
| API calls return 404 | URL rewriting not enabled | Enable `mod_rewrite` and confirm `AllowOverride All` on the vhost |
| Database connection fails on "localhost" | Socket connection not supported in this context | Use an IP address such as `127.0.0.1` as the Host Name instead |
| Internal app files accessible via browser | Vhost points to app root instead of `public` | Repoint `DocumentRoot` to the app's `public` folder |
| Scheduled tasks never run | Cron job not configured | Add the scheduler cron entry per [Section 8.1](#81-cron-job-scheduled-tasks) |
| Background tasks stuck on "Pending" | Messenger worker not running | Start and persist the queue worker per [Section 8.2](#82-messenger-worker-async-tasks) |
| Install halts on a required check | A mandatory system requirement failed | Resolve the specific requirement shown, then click RECHECK |
| `composer install` fails on PHP version | Local PHP version doesn't match `composer.json` constraints | Confirm your PHP version against [Section 3.1](#31-platform) and switch versions if needed |
| Front-end build fails or produces no assets in `public` | Node/yarn version mismatch, or `yarn build` not run | Confirm Node/yarn versions match [Section 3.3](#33-front-end-build-tools), then re-run `yarn install && yarn build` |

---

## 10. Support Contact

**RahbarCRM — Powered by Fynsis Softlabs**

- **Website:** [rahbarcrm.com](https://rahbarcrm.com)
- **Support:** [support@fynsis.com](mailto:support@fynsis.com)
- **Implementation & Sales:** [sales@fynsis.com](mailto:sales@fynsis.com)
- **Source:** [github.com/FynsisSoftlabs/rahbarcrm](https://github.com/FynsisSoftlabs/rahbarcrm)
