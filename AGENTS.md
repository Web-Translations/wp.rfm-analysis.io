# Repository Guidelines

## Project Structure & Module Organization
Bedrock uses the Roots layout. `web/` is the document root served by nginx: `web/wp` contains WordPress core, while `web/app` holds project code (mu-plugins, plugins, themes, uploads). Environment configuration lives in `config/`, with base settings in `application.php` and overrides in `config/environments/`. Docker assets sit under `docker/`, and service definitions reside in `compose.yaml`. Composer manages PHP dependencies in `vendor/`.

## Build, Test, and Development Commands
- `composer install` — install PHP dependencies and prepare the Bedrock autoloader.
- `docker compose up --build` — start nginx, PHP-FPM, MariaDB, and phpMyAdmin locally on ports 8080/8081/3306.
- `docker compose exec fpm composer lint` — run Laravel Pint in test mode against custom PHP code.
- `docker compose exec fpm vendor/bin/wp plugin list` — confirm WordPress boots and plugins register inside the container.

## Coding Style & Naming Conventions
PHP should follow Pint's `per` preset (PSR-12 base with project tweaks). Use 4-space indentation, descriptive kebab-case folder names (e.g., `web/app/plugins/acme-analytics`), and snake_case config keys. Run `composer lint:fix` before committing. Store secrets via templated `.env` files; never commit real credentials.

## Testing Guidelines
No automated PHPUnit suites exist yet, so rely on linting plus targeted WordPress smoke checks. Add coverage with WP-CLI scripts or integration tests under `web/app/plugins/<feature>/tests` when adding complex logic. Name tests after the behaviour they assert (e.g., `ActivatingFeatureTest.php`) and include manual verification notes in PRs until suites exist.

## Commit & Pull Request Guidelines
Keep commit subjects short and imperative, mirroring `Add composer.lock` and `Fix env variables`. Each PR should link relevant issues, summarise the change, list affected services (nginx, FPM, database), and document regression steps or screenshots for UI updates. Mention any manual validation performed because CI is limited.

## Environment & Security Notes
Copy `.env.example` to `.env` and fill database credentials, URLs, and salts per environment. Configure `WP_ENV`, `WP_HOME`, and `WP_SITEURL` via host environment variables in production. Keep non-production environments behind HTTPS and retain `roots/bedrock-disallow-indexing` to block search indexing.
