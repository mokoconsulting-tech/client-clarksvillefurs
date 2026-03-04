# Copilot Coding Agent Instructions

## Repository Overview

This is a **client implementation** of the [MokoCassiopeia](https://github.com/mokoconsulting-tech/MokoCassiopeia) Joomla template for **Clarksville Furs**. MokoCassiopeia is a lightweight enhancement layer on top of Joomla's built-in Cassiopeia template, adding Font Awesome 7, Bootstrap 5 helpers, dark mode theming, a Table of Contents utility, and optional Google Tag Manager / GA4 integration.

**Client constraint:** Only documentation files (`README.md`, `CHANGELOG.md`, `docs/*.md`) carry client-specific content. Configuration files (`templateDetails.xml`, `updates.xml`) remain in base template state unless explicitly required for the client.

## Tech Stack

- **Language:** PHP 8.0+
- **Platform:** Joomla 4.4.x / 5.x (extends Cassiopeia template)
- **Frontend:** Bootstrap 5, Font Awesome 7 Free
- **Standards:** PSR-12, Joomla Coding Standards
- **Testing:** Codeception (acceptance + unit)
- **Static Analysis:** PHPStan level 5
- **Linting:** PHP_CodeSniffer (PSR-12 + PHPCompatibility)

## Project Layout

```
.github/                  # GitHub config (copilot.yml, dependabot.yml, CODEOWNERS)
src/
  images/                 # Template images
  media/                  # CSS, JS, vendor assets (bootstrap-toc, fa7free)
docs/                     # Developer documentation
templates/                # Color palette template files (not committed to VCS)
tests/
  acceptance/             # Codeception acceptance tests
  unit/                   # Codeception unit tests
  acceptance.suite.yml
  unit.suite.yml
codeception.yml           # Codeception configuration
phpcs.xml                 # PHP_CodeSniffer ruleset (PSR-12 + PHPCompatibility, targets src/)
phpstan.neon              # PHPStan config (level 5, targets src/, PHP 8.0)
.editorconfig             # Tab indentation (tab_width=2), LF line endings
```

Key source paths scanned by quality tools: `src/` only. Tests output goes to `tests/_output/`.

## Build & Validation Commands

No Makefile is currently present. Use the following commands directly.

### Install development dependencies (run once)

```bash
composer global require "squizlabs/php_codesniffer:^3.0" --with-all-dependencies
composer global require "phpstan/phpstan:^1.0" --with-all-dependencies
composer global require "phpcompatibility/php-compatibility:^9.0" --with-all-dependencies
composer global require "codeception/codeception" --with-all-dependencies
phpcs --config-set installed_paths ~/.composer/vendor/phpcompatibility/php-compatibility
```

### Lint / code style

```bash
# Check code style (PSR-12 + PHPCompatibility, checks src/)
phpcs --standard=phpcs.xml

# Auto-fix fixable issues
phpcbf --standard=phpcs.xml
```

### Static analysis

```bash
phpstan analyse --configuration=phpstan.neon
```

### PHP syntax check

```bash
find src -name "*.php" -exec php -l {} \;
```

### Tests

```bash
# Run all tests
codecept run

# Run unit tests only
codecept run unit

# Run acceptance tests only (requires Joomla + MySQL)
codecept run acceptance
```

> **Note:** Acceptance tests require a running Joomla instance with MySQL. Unit tests can run standalone. The default database DSN in `codeception.yml` is `mysql:host=localhost;dbname=joomla_test`; override credentials via environment variables rather than editing the file directly.

## Coding Standards

- **Indentation:** Tabs (tab_width = 2) per `.editorconfig`
- **Line endings:** LF
- **PHP style:** PSR-12 enforced via PHP_CodeSniffer; no backtick operators
- **Constants:** UPPER_CASE
- **File headers:** All source files must include the SPDX license header (`SPDX-License-Identifier: GPL-3.0-or-later`) and the Moko Consulting file metadata block
- **No version bumps** in source files unless explicitly requested

## CI / Pre-commit Checks

There are no GitHub Actions workflows in this client repository. Quality checks must be run locally before committing:

1. PHP syntax check (`php -l`)
2. PHP_CodeSniffer (`phpcs --standard=phpcs.xml`)
3. PHPStan (`phpstan analyse --configuration=phpstan.neon`)

Refer to `docs/JOOMLA_DEVELOPMENT.md` for full development guide and `docs/WORKFLOW_GUIDE.md` for branching strategy (`dev/X.Y.Z` → `rc/X.Y.Z` → `version/X.Y.Z` → `main`).
