# What This Repo Is

This is a **client implementation** of the [MokoCassiopeia](https://github.com/mokoconsulting-tech/MokoCassiopeia) Joomla template for **Clarksville Furs**, hosted at <https://github.com/mokoconsulting-tech/client-clarksvillefurs>. It contains the Clarksville Furs–specific CSS color palettes, branding images, and documentation layered on top of the base MokoCassiopeia template. It is **not** the base template itself, **not** a standalone library, and **not** a monorepo root — the Joomla PHP template engine lives in the upstream [MokoCassiopeia](https://github.com/mokoconsulting-tech/MokoCassiopeia) repository. Client-specific content is restricted to documentation files (`README.md`, `CHANGELOG.md`, `docs/*.md`) and the CSS/image assets under `src/`; configuration files (`templateDetails.xml`, `updates.xml`) stay in base-template state unless a client change is explicitly required.

# Repo Structure

```
.github/                  # GitHub config: copilot.yml, dependabot.yml, CODEOWNERS,
                          #   copilot-instructions.md, pull_request_template.md
src/
  images/branding/        # Client branding assets (logo, mascot, background images)
  media/css/colors/       # Client custom CSS color palettes (light/ and dark/)
docs/                     # Developer documentation (see Key Policy Documents)
templates/                # Color palette CSS template files; excluded from VCS (.gitignore)
tests/
  acceptance/             # Codeception acceptance tests (require Joomla + MySQL)
  unit/                   # Codeception unit tests (run standalone)
  _data/                  # Test fixtures and SQL dumps
  _output/                # Test reports and screenshots (gitignored)
  _support/               # Codeception helper classes
  acceptance.suite.yml    # Acceptance suite config
  unit.suite.yml          # Unit suite config
codeception.yml           # Codeception global config (MySQL DSN, coverage paths)
phpcs.xml                 # PHP_CodeSniffer ruleset — PSR-12 + PHPCompatibility, targets src/
phpstan.neon              # PHPStan config — level 5, targets src/, PHP 8.0
.editorconfig             # Tab indentation (tab_width=2), LF line endings
MokoStandards.override.tf # MokoStandards sync override — prevents bulk-sync from
                          #   overwriting repo-specific files
```

# File Header Requirements

Every new source file must include a license header. Two forms are used — choose based on context.

## Minimal Header

Used for source code, CSS, and regular documentation files:

```
Copyright (C) <YEAR> Moko Consulting <hello@mokoconsulting.tech>

This file is part of a Moko Consulting project.

SPDX-License-Identifier: GPL-3.0-or-later

# FILE INFORMATION
DEFGROUP: <dot.separated.group>
INGROUP: <dot.separated.parent.group>
REPO: https://github.com/mokoconsulting-tech/client-clarksvillefurs
PATH: /<path/from/repo/root/filename.ext>
VERSION: <semver e.g. 03.06.03>
BRIEF: <One-sentence description of this file's purpose>
```

## Full Header

Used for governance, policy, and automation files (e.g., `.tf`, main documentation indices). Adds the complete GPL program notice between the SPDX line and the FILE INFORMATION block:

```
Copyright (C) <YEAR> Moko Consulting <hello@mokoconsulting.tech>

This file is part of a Moko Consulting project.

SPDX-License-Identifier: GPL-3.0-or-later

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program. If not, see <https://www.gnu.org/licenses/>.

# FILE INFORMATION
DEFGROUP: ...
```

## Comment Syntax by File Type

| Extension        | Comment wrapper                          |
| ---------------- | ---------------------------------------- |
| `.php`           | `<?php` then `/* ... */` or `//`         |
| `.css`           | `@charset "UTF-8";` then `/* ... */`     |
| `.md`            | `<!-- ... -->`                           |
| `.tf`, `.sh`     | `# ...` line comments                    |
| `.yml` / `.yaml` | `# ...` line comments                    |

## FILE INFORMATION Block Fields

| Field      | Required | Notes                                                              |
| ---------- | -------- | ------------------------------------------------------------------ |
| `DEFGROUP` | Yes      | Dot-separated group identifier, e.g. `Joomla.Template.Site`       |
| `INGROUP`  | Yes      | Parent group, e.g. `MokoCassiopeia.Documentation.Client.ClarksvilleFurs` |
| `REPO`     | Yes      | Full HTTPS URL of this repository                                  |
| `PATH`     | Yes      | Absolute path from repo root, e.g. `/src/media/css/colors/light/colors_custom.css` |
| `VERSION`  | Yes      | Current template version, e.g. `03.06.03`; do not bump unless asked |
| `BRIEF`    | Yes      | One-sentence description of this specific file's purpose           |
| `NOTE`     | No       | Optional extra context; used in a few governance files             |

## Exemptions

JSON files, binary files (images, compiled assets), and generated files (test output, dist artifacts) do not require a header. Files under `tests/_output/` are gitignored and exempt.

# Coding Standards

## Indentation and Whitespace

- **All file types**: tabs, visual width = 2 (`.editorconfig` `indent_style = tab`, `tab_width = 2`)
- **Line endings**: LF for all files; CRLF only for `.ps1`, `.bat`, `.cmd`
- **Trailing whitespace**: stripped on save (except `.md` files, where trailing spaces are line breaks)
- **Final newline**: always insert

## Line Length

No enforced maximum — `Generic.Files.LineLength` is explicitly excluded in `phpcs.xml`. Keep lines readable in context.

## Naming Conventions

| Target         | Convention  | Source                                       |
| -------------- | ----------- | -------------------------------------------- |
| PHP constants  | `UPPER_CASE`| `phpcs.xml` `Generic.NamingConventions.UpperCaseConstantName` |
| PHP classes    | `PascalCase`| PSR-12                                       |
| PHP methods    | `camelCase` | PSR-12                                       |
| PHP variables  | `camelCase` | PSR-12                                       |
| CSS custom props | `--kebab-case` | Existing codebase pattern                |
| Files (PHP)    | `PascalCase.php` for classes | PSR-12 / Joomla convention     |

## Primary Language

PHP for all new source and test code. CSS for theme customizations. No new JavaScript files are introduced in this client repo (JS lives in the upstream base template).

# Language-Specific Requirements

## PHP

**Runtime target:** PHP 8.0+ (`phpstan.neon` `phpVersion: 80000`, `phpcs.xml` `testVersion value="8.0-"`)

**Static analysis:** PHPStan level 5 (`phpstan.neon` `level: 5`). All new code must pass without new errors.

**Style:** PSR-12 enforced via PHP_CodeSniffer (`phpcs.xml`). Key specifics:
- No backtick operators (`Generic.PHP.BacktickOperator` is active)
- No deprecated PHP functions (`Generic.PHP.DeprecatedFunctions` is active)
- One statement per line (`Generic.Formatting.DisallowMultipleStatements`)
- Duplicate class names forbidden (`Generic.Classes.DuplicateClassName`)

**Docblocks:** Use standard PHPDoc blocks on classes and public methods. Required sections: `@param` (with type), `@return` (with type or `void`). Example from `tests/unit/TemplateConfigurationTest.php`:

```php
/**
 * Test that template has valid configuration structure
 */
public function testTemplateManifestExists(): void
{
    // ...
}
```

**Test structure:** Codeception tests live in `tests/unit/` (class-based, extends `Codeception\Test\Unit`) and `tests/acceptance/` (Cest pattern, uses `AcceptanceTester`). Database credentials for acceptance tests are read from environment variables — do not hard-code credentials.

# Commit Message Format

This repository follows [Conventional Commits](https://www.conventionalcommits.org/). No `.gitmessage` file exists; the format is enforced by convention and the PR checklist.

## Structure

```
<type>(<optional scope>): <subject>

[optional body]

[optional footer(s)]
```

## Valid Types

| Type       | When to use                                              |
| ---------- | -------------------------------------------------------- |
| `feat`     | New feature or capability                                |
| `fix`      | Bug fix                                                  |
| `docs`     | Documentation-only change                               |
| `style`    | Formatting, whitespace — no logic change                 |
| `refactor` | Code restructuring without feature or fix                |
| `test`     | Adding or updating tests                                 |
| `chore`    | Maintenance (deps, config, tooling)                      |
| `ci`       | CI/CD workflow changes                                   |

## Rules

- Subject line: imperative mood, lowercase start, no trailing period, ≤ 72 characters
- Body: wrap at 72 characters, explain *what* and *why*
- Breaking changes: add `BREAKING CHANGE:` footer or `!` after type, e.g. `feat!:`
- Reference issues with `Closes #<n>` or `Refs #<n>` in footer

**Example:**
```
feat(colors): add Clarksville Furs sky-blue to light palette

Extracted #a3cde2 from mascot background image and mapped it to
--color-primary in colors_custom.css for light mode.
```

# Running Validation

Run these commands before every commit. No Makefile is present; use the commands directly.

## Install Development Dependencies (once)

```bash
composer global require "squizlabs/php_codesniffer:^3.0" --with-all-dependencies
composer global require "phpstan/phpstan:^1.0" --with-all-dependencies
composer global require "phpcompatibility/php-compatibility:^9.0" --with-all-dependencies
composer global require "codeception/codeception" --with-all-dependencies
phpcs --config-set installed_paths ~/.composer/vendor/phpcompatibility/php-compatibility
```

## Pre-commit Checks (run every time)

```bash
# 1. PHP syntax check
find src -name "*.php" -exec php -l {} \;

# 2. Code style (PSR-12 + PHPCompatibility)
phpcs --standard=phpcs.xml

# Auto-fix fixable violations
phpcbf --standard=phpcs.xml

# 3. Static analysis
phpstan analyse --configuration=phpstan.neon
```

## Tests

```bash
# Unit tests (no Joomla instance required)
codecept run unit

# All tests (acceptance requires running Joomla + MySQL)
codecept run

# Override DB credentials via env (do not edit codeception.yml)
DB_DSN="mysql:host=myhost;dbname=mydb" DB_USER=myuser DB_PASS=mypass codecept run acceptance
```

# Contribution Workflow

1. **Fork** the repository on GitHub.
2. **Create a version branch** from the active dev branch using the naming convention `dev/X.Y.Z` (e.g., `dev/03.07.00`). Never commit directly to `main`, `rc/*`, or `version/*`.
3. **Set up tools** using the install commands in [Running Validation](#running-validation).
4. **Make focused, minimal changes** targeting a single concern. Do not bump version numbers unless explicitly asked.
5. **Validate** before every commit:
   ```bash
   find src -name "*.php" -exec php -l {} \;
   phpcs --standard=phpcs.xml
   phpstan analyse --configuration=phpstan.neon
   ```
6. **Commit** using Conventional Commits format (see [Commit Message Format](#commit-message-format)).
7. **Push** the branch and open a Pull Request against the active `dev/X.Y.Z` branch (not `main`).
8. **Release flow**: `dev/X.Y.Z` → `rc/X.Y.Z` → `version/X.Y.Z` → `main`. Branch promotion is handled via GitHub Actions release pipeline, not manual merges.

**Merge strategy:** Pull requests are merged (not squashed) into their target version branches; final promotion to `main` is via the automated release pipeline.

# PR Checklist

From `.github/pull_request_template.md` plus repo standards:

- [ ] Follows Conventional Commits format for all commits
- [ ] PHP syntax passes: `find src -name "*.php" -exec php -l {} \;`
- [ ] PHPCS passes: `phpcs --standard=phpcs.xml`
- [ ] PHPStan passes: `phpstan analyse --configuration=phpstan.neon`
- [ ] Tests added or updated for any changed functionality
- [ ] Documentation updated where required (keep `docs/*.md` in sync with code)
- [ ] SPDX license header present in every new source file
- [ ] File header VERSION field matches current template version (do not bump unless asked)
- [ ] No secrets, credentials, or environment-specific values committed
- [ ] `templateDetails.xml` and `updates.xml` left in base-template state unless a client change was explicitly requested
- [ ] Linked GitHub issue(s) referenced in PR description

# What NOT to Do

- **Never commit** `configuration.php`, `.env`, `*.secret.php`, or any credential file — these are gitignored for a reason.
- **Never commit** `dist/`, `release/`, or ZIP archives — these are build artifacts.
- **Never bump version numbers** in source files (`templateDetails.xml`, `updates.xml`, FILE INFORMATION VERSION fields) unless the task explicitly requests it.
- **Never modify** `templateDetails.xml` or `updates.xml` for client-specific content unless explicitly required; those files stay in base-template state per the client-constraint rule.
- **Never use backtick operators** in PHP — blocked by `Generic.PHP.BacktickOperator` in `phpcs.xml`.
- **Never commit directly** to `main`, `rc/*`, or `version/*` branches.
- **Never skip** the three pre-commit validation steps (php -l, phpcs, phpstan) before pushing.

# Key Policy Documents

| Document | Purpose |
| -------- | ------- |
| [CONTRIBUTING.md](./CONTRIBUTING.md) | Contribution workflow, coding standards, commit conventions |
| [docs/WORKFLOW_GUIDE.md](./docs/WORKFLOW_GUIDE.md) | Git branching strategy, CI/CD workflows, release pipeline |
| [docs/JOOMLA_DEVELOPMENT.md](./docs/JOOMLA_DEVELOPMENT.md) | Local dev setup, PHP quality tools, Codeception testing |
| [docs/QUICK_START.md](./docs/QUICK_START.md) | First-time contributor setup in 5 minutes |
| [docs/CSS_VARIABLES.md](./docs/CSS_VARIABLES.md) | All CSS custom properties and color palette customization |
| [SECURITY.md](./SECURITY.md) | Security vulnerability reporting (do not use public issues) |
| [CHANGELOG.md](./CHANGELOG.md) | Version history; update Unreleased section with every change |
