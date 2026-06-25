# Changelog

All notable changes to **Cetus Image Converter & AI Alt Text** are documented in this file.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) and the project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.5.3] – 2026-06-23

### Fixed

- **Featured image missing srcset** — `rebuild_srcset_if_missing()` now receives the attachment ID directly from the `post_thumbnail_html` filter (5-parameter signature) instead of parsing the `wp-image-{id}` CSS class, which themes often omit. Srcset is correctly regenerated from `wp_get_attachment_metadata()` with per-file `file_exists()` verification and original fallback.
- **Lightbox href sync** — `sync_lightbox_hrefs()` now aligns `<a href>` to the optimised format of the wrapped `<img src>`, preventing the Lightbox from loading the heavy original JPEG at full screen.
- **src/srcset format asymmetry in content** — `sync_img_attributes()` detects when `srcset` already contains `.webp`/`.avif` entries but `src` still points to `.jpg`/`.png` and promotes `src` to the optimised format (verified on disk before applying).
- **data-src support** — lazy-loading attributes are now included in the sync/rollback pass alongside `src` and `srcset`.

---

## [1.5.2] – 2026-06-22

### Fixed

- **Theme URL interference** — all frontend filters (`wp_get_attachment_image_src`, `wp_calculate_image_srcset`, `wp_filter_content_tags`) now run at `PHP_INT_MAX` priority, after any theme filter that manipulates image URLs.
- **Black squares and 404s from theme extension swapping** — new `sanitize_optimized_urls()` filter on `the_content` and `post_thumbnail_html` (also at `PHP_INT_MAX`) checks every `.avif`/`.webp` URL against the filesystem. If the optimised file does not exist (e.g. a thumbnail size the theme substituted but Cetus never converted), the original `.jpg`/`.png` URL is restored automatically.

---

## [1.5.1] – 2026-06-22

### Fixed

- **GD fallback crash on corrupt files** — `convert_with_gd()` now validates the file with `getimagesize()` before passing it to GD. Files that are corrupt or have a mismatched extension (e.g. a JPEG renamed `.png`) are skipped cleanly instead of triggering a PHP Warning captured by Sentry.
- **GD MIME detection from extension** — switched from `wp_check_filetype()` (extension-based) to `getimagesize()` (content-based) so the correct GD function is always called regardless of the file extension.
- **Thumbnail size conversion not working in bulk optimizer** — `build_queue()` and `convert_attachment_sizes()` now locate size files via `glob()` on the filesystem instead of reconstructing paths from `_wp_attachment_metadata`, which on some hosts (e.g. Aruba) stores absolute paths and caused all size conversions to be silently skipped.
- **Orphan scanner incorrectly flagging registered thumbnail sizes as orphans** — `get_all_registered_paths()` now normalises `_wp_attachment_metadata['file']` to a relative path before constructing size paths, fixing false positives on hosts that store absolute paths in metadata.
- **Bulk optimizer ignoring WebP attachments** — `build_queue()` now queries `image/webp` in addition to JPEG/PNG/GIF, so existing WebP images are included when the target format is AVIF (WebP → AVIF re-conversion).
- **Admin header** — plugin version, Docs, Support, GitHub Issues and Email links added next to the page title.

---

## [1.5.0] – 2026-06-22

First public release of **Cetus Image Converter & AI Alt Text**.

### Added

- **AVIF / WebP conversion** — creates optimised copies alongside originals using Imagick (with GD fallback). Original files are never deleted.
- **Smart format selection** — *Automatic* mode picks AVIF when the server supports it, WebP otherwise. Force a specific format or disable conversion entirely.
- **Auto-convert on upload** — optionally convert every new image the moment it lands in the media library.
- **AI-powered Alt Text** — sends images to Google Gemini or OpenAI (BYOK) and saves the generated description as `_wp_attachment_image_alt`. Automatic provider fallback on HTTP 429.
- **Bulk processor** — asynchronous AJAX batches (5 images per tick) with real-time progress bar, Pause, and Stop controls. Safe for shared hosting.
- **Orphan file scanner** — finds images on disk not registered in the WordPress database and optimises them via a tick-based AJAX system with progress bar. Handles large libraries (2000+ files) without timeouts.
- **WP-Cron background processing** — processes 5 images every 5 minutes without keeping the browser open.
- **Quality sliders** — WebP (default 82) and AVIF (default 75) configurable independently.
- **Conversion log** — scrollable table of the last 50 conversions (date, filename, format, space saved, status). Clearable via AJAX.
- **Per-image exclusion** — meta box on the attachment edit screen to skip individual images from conversion and Alt Text generation.
- **Cumulative savings counter** — total bytes saved across all sessions, persisted in the database.
- **Statistics cards** — total images, images to convert, disk usage, savings.
- **"Delete all converted files" button** — removes every WebP/AVIF file generated by the plugin without touching originals.
- **WebP → AVIF re-conversion** — WebP images already in the library can be re-converted to AVIF.
- **Frontend URL replacement** — `wp_get_attachment_image_src`, `wp_calculate_image_srcset`, and `wp_filter_content_tags` filters swap JPG/PNG URLs with WebP/AVIF equivalents. No `.htaccess` rules required for URL rewriting.
- **Automatic MIME type registration** — writes `AddType image/avif .avif` and `AddType image/webp .webp` to `.htaccess` on activation. Fixes AVIF/WebP files downloading instead of displaying on Apache shared hosting (e.g. Aruba). Rules are removed on deactivation.
- **Server Diagnosis panel** — green/red indicators for Imagick, GD, WebP, and AVIF support with a real encoding probe (1×1 pixel test, cached 24 h).
- **Three-step onboarding wizard** — Diagnosis → Preferences → Library Management.
- **AI Alt Text language selector** — auto-detected from WordPress locale (22 languages) or manually overridden. Custom prompt textarea.
- **Opt-in anonymous crash reporting** — via Sentry (disabled by default). Stack traces filtered to plugin files only; no IP, username, or personal data transmitted.
- **Internationalisation** — English source strings; `it_IT`, `es_ES`, `fr_FR`, `de_DE` translations included.
- **Migrator** — automatic migration from legacy v1.x options.
