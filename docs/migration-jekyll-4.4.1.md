# Jekyll Migration: Upgrade to 4.4.1 and Sass Deprecation Fix

## Overview

This report summarizes the migration of the blog project to Jekyll 4.4.1 and the accompanying Sass/Dart Sass compatibility fixes. The goal was to update the site generator, eliminate Sass deprecation warnings, and restore a clean local build.

## Pre-Migration Assessment

### Current project state
- Jekyll site powered by `jekyll-theme-tactile` and custom Minima Sass files
- Ruby environment in use: `3.2.11`
- Build managed with Bundler and `vendor/bundle`
- Sass converter using Dart Sass via `jekyll-sass-converter`

### Compatibility check
- Confirmed Jekyll 4.4.1 is compatible with the existing Ruby and plugin stack
- Identified the migration risk area as Sass module and division deprecation changes introduced by Dart Sass

## Migration Steps

### 1. Lock Jekyll version
- Updated `Gemfile` to use:
  ```ruby
  gem "jekyll", "~> 4.4"
  ```
- Ran `bundle update jekyll` to install Jekyll 4.4.1 and resolve dependencies in `Gemfile.lock`

### 2. Verify dependency tree
- Checked that `jekyll-feed`, `jekyll-seo-tag`, `jekyll-sitemap`, and theme dependencies remained compatible
- Confirmed `bundle exec jekyll build` reached the Sass compilation stage successfully

### 3. Convert Sass to Dart Sass module-compatible syntax
- Updated the main stylesheet entrypoint in `assets/css/style.scss` to use Sass module imports and include theme partials
- Converted legacy Sass `@import` patterns in `_sass/minima/initialize.scss` to `@use`
- Added `@use "sass:math"` in partials needing division operations
- Replaced deprecated slash-based division with `math.div(...)` in `_sass/minima/_base.scss` and `_sass/minima/_layout.scss`
- Replaced legacy `lighten()`/`darken()` calls with `color.adjust(...)` in `_sass/minima/skins/dark.scss` and `_sass/minima/skins/classic.scss`

### 4. Fix Sass variable scoping
- Introduced a shared variables partial in `_sass/minima/custom-variables.scss`
- Ensured every Minima partial that referenced theme variables imported the shared variables and the active skin declaration
- Added `@use "minima/skins/dark" as *;` to partials that relied on theme colors and design tokens

### 5. Repair syntax issues caused during migration
- Fixed a malformed block in `_sass/minima/_layout.scss` where `.footer-heading` was missing its closing brace
- Ensured all Sass partials had valid top-level `@use` placement and no dynamic `@use` paths

## Issues Encountered and Resolutions

### Sass undefined variable errors
- **Problem**: Build failed with `Undefined variable` in `_sass/minima/_base.scss` and later `_sass/minima/_layout.scss`
- **Cause**: Theme variables were not visible in module-scoped partials after switching to `@use`
- **Fix**: Explicitly imported `minima/custom-variables` and `minima/skins/dark` into dependent partials

### Sass syntax error in layout partial
- **Problem**: Build failed with `expected "}"` in `_sass/minima/_layout.scss`
- **Cause**: A missing closing brace in the `.footer-heading` block introduced during earlier edits
- **Fix**: Corrected the block structure and added the missing `}`

### Bundler DidYouMean deprecation warning
- **Problem**: Commands such as `bundle --version` and `bundle exec ruby -v` printed:
  - `DidYouMean::SPELL_CHECKERS.merge!` deprecation warning
- **Cause**: Project runtime still used old Bundler `2.2.32` (vendored Thor code), which calls deprecated DidYouMean API on Ruby `3.2.11`.
- **Fix**:
  - Installed a modern Bundler version (`4.0.10`)
  - Ran `bundler _4.0.10_ update --bundler` to rewrite `Gemfile.lock` metadata
  - Removed stale vendored gems (`vendor/bundle`) and reinstalled dependencies with the new Bundler context
- **Result**:
  - `Gemfile.lock` now shows `BUNDLED WITH 4.0.10`
  - `bundle exec ruby -v`, `bundle exec jekyll build`, and `bundle exec jekyll serve --drafts` run without the DidYouMean deprecation warning

### GitHub Pages deployment pipeline mismatch (dark mode regression)
- **Problem**: After deployment, the public site rendered without dark theme styles, while local builds looked correct.
- **Cause**:
  - GitHub Pages native build path used an older Jekyll runtime than local development.
  - Deployed stylesheet output did not match the local Sass module pipeline, resulting in missing compiled dark-theme rules.
- **Fix**:
  - Introduced GitHub Actions deployment workflow at `.github/workflows/pages.yml`.
  - Standardized production build on Ruby `3.2.11` and Jekyll `4.4.1` via `ruby/setup-ruby` and `bundle exec jekyll build`.
  - Configured PRs to `master` for build-only validation and pushes to `master` for artifact deployment to Pages.
  - Switched GitHub Pages source to **GitHub Actions** to avoid native branch-based Jekyll build.
- **Result**:
  - Production now uses the same build toolchain as local.
  - Dark theme styles are rendered correctly on the deployed site.
  - CSS output is consistently compiled from the project Sass pipeline.

## Validation and Verification

### Build verification
- Executed `bundle exec jekyll build`
- Confirmed the build completed successfully with no Sass conversion errors

### Output status
- Site generation completed without runtime Sass warnings
- Build exit code: `0`
- Bundler DidYouMean deprecation warning no longer appears in build/serve startup output

## Files changed

- `Gemfile`
- `Gemfile.lock`
- `.github/workflows/pages.yml`
- `assets/css/style.scss`
- `_sass/minima/initialize.scss`
- `_sass/minima/_base.scss`
- `_sass/minima/_layout.scss`
- `_sass/minima/custom-variables.scss`
- `_sass/minima/custom-styles.scss`
- `_sass/minima/skins/dark.scss`
- `_sass/minima/skins/classic.scss`
- `README.md`
- `.github/copilot-instructions.md`

## Lessons learned

- Dart Sass requires all `@use` rules at the top of a stylesheet and does not support dynamic import paths.
- Sass variable sharing across modules must be explicit; `@use` does not automatically expose variables to imported files.
- Legacy Sass division and color helper functions must be migrated to `sass:math` and `sass:color` style calls for compatibility with modern Sass.

## Recommendations

- Keep `jekyll` locked to `~> 4.4` and monitor future minor patch releases.
- Avoid mixing legacy `@import` and module `@use` patterns in the same stylesheet set.
- Add a lightweight style regression check after future Sass updates to catch variable scope or syntax regressions early.
