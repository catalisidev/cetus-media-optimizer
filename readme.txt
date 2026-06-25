=== Cetus Image Converter & AI Alt Text ===
Contributors:      catalisi, danielegagliardi
Tags:              webp, avif, image optimization, alt text, ai
Requires at least: 6.2
Tested up to:      7.0
Stable tag:        1.5.6
Requires PHP:      8.0
License:           GPL-2.0-or-later
License URI:       https://www.gnu.org/licenses/gpl-2.0.html

Advanced image optimizer: convert to AVIF/WebP, auto-generate Alt Text via AI, detect orphan files, and manage your entire media library.

== Description ==

**Cetus Image Converter & AI Alt Text** turns your WordPress media library into a lean, modern, and accessible asset store.

= What it does =

* **AVIF / WebP conversion** — Creates optimised copies alongside your originals (JPG/PNG/GIF are never deleted). Uses Imagick when available and falls back to GD.
* **Smart format selection** — *Automatic* mode picks AVIF when the server supports it, WebP otherwise. You can also force one format.
* **Auto-convert on upload** — Optionally convert every new image the moment it lands in your library.
* **AI-powered Alt Text** — Send images to Google Gemini or OpenAI (your own API key, BYOK) and save the generated description natively as `_wp_attachment_image_alt`. Automatic fallback between providers on quota errors (429).
* **Orphan file scanner** — Finds images on disk that are not registered in the WordPress database and lets you optimise or ignore them.
* **Bulk processor** — Asynchronous AJAX batches with real-time progress bar, Pause and Stop controls. Safe for shared hosting.

= Privacy & Security =

* All AJAX requests are protected by WordPress nonces and `current_user_can()` checks.
* Original images are **never deleted** — conversion only adds new files.
* Telemetry is **opt-in only** and disabled by default. No data is ever sent unless the administrator explicitly enables it under Preferences → Telemetry.
* API keys are stored in the WordPress options table and are never exposed in the front-end source.

= Server requirements =

| Feature        | Requirement                              |
| -------------- | ---------------------------------------- |
| WebP output    | Imagick with WebP **or** GD with WebP    |
| AVIF output    | Imagick with libavif/libheif             |
| AI Alt Text    | A valid Google Gemini or OpenAI API key  |

The built-in **Step 1 – Server Diagnosis** panel shows green/red indicators for every capability on your specific server.

= BYOK — Bring Your Own Key =

Cetus Image Converter & AI Alt Text does **not** proxy AI requests through any third-party server. Calls go directly from your WordPress installation to the Google Generative Language API or the OpenAI API using your own credentials. You are responsible for monitoring your quota usage.

== External Services ==

This plugin connects to the following third-party services:

**Google Gemini (Google Generative Language API)**
Used to automatically generate Alt Text for images. Only called when the administrator has entered a valid Google Gemini API key and triggers a conversion (manual bulk process or automatic on upload, if enabled). The image is sent as a Base64-encoded inline payload. No image data is stored on any Catalisi server.

* What is sent: image data (Base64), custom prompt (if set), language preference
* When: only on explicit user action (bulk process or single upload with auto-convert enabled)
* Google Terms of Service: https://policies.google.com/terms
* Google Privacy Policy: https://policies.google.com/privacy
* Google Generative AI Terms: https://ai.google.dev/gemini-api/terms

**OpenAI API**
Used to automatically generate Alt Text for images. Only called when the administrator has entered a valid OpenAI API key and triggers a conversion. The image is sent as a Base64-encoded inline payload. No image data is stored on any Catalisi server.

* What is sent: image data (Base64), custom prompt (if set), language preference
* When: only on explicit user action (bulk process or single upload with auto-convert enabled)
* OpenAI Terms of Service: https://openai.com/policies/terms-of-use/
* OpenAI Privacy Policy: https://openai.com/policies/privacy-policy/

**Sentry**
Used to collect anonymous crash reports (PHP fatal errors and exceptions). This feature is **disabled by default** and requires explicit opt-in by the site administrator under Cetus Media → Preferences → Telemetry.

* What is sent: PHP error type and stack trace (limited to plugin files only), WordPress version, PHP version, plugin version. No IP addresses, usernames, email addresses or any personal data are ever transmitted.
* When: only when the administrator explicitly enables telemetry opt-in
* Sentry Terms of Service: https://sentry.io/terms/
* Sentry Privacy Policy: https://sentry.io/privacy/

== Installation ==

1. Upload the `cetus-media-optimizer` folder to `/wp-content/plugins/`.
2. Activate the plugin through the **Plugins** screen in WordPress.
3. Go to **Cetus Media** in the WordPress admin menu.
4. Follow the three-step wizard:
   - **Step 1** — Review the server diagnostics (green = supported, red = unavailable).
   - **Step 2** — Choose your output format and optionally add AI API keys.
   - **Step 3** — Click *Start Optimisation* to process your library.

== Frequently Asked Questions ==

= Will Cetus Image Converter & AI Alt Text delete my original images? =

No. Originals are never touched. The plugin creates a second file with the new extension (e.g. `photo.jpg` → `photo.webp`) alongside the original.

= My server does not support AVIF. What happens? =

If you choose *Automatic* format, the plugin detects server capabilities at runtime and falls back to WebP. If WebP is also unavailable, the image is skipped and a notice is shown in the diagnostics panel.


= Is there a limit to the number of images I can process? =

No hard limit. The bulk processor works in small batches (5 images per AJAX tick) to respect server timeouts and can be paused and resumed at any time.

= How does the AI Alt Text feature work? =

After conversion, the plugin sends the image (as a Base64-encoded inline payload) to the selected AI provider. The response is sanitised and saved as the standard WordPress `_wp_attachment_image_alt` post meta. No image data is stored on any Catalisi server.

= What happens if my AI quota is exceeded? =

If the primary provider returns HTTP 429, the plugin automatically retries with the other provider (if both keys are configured and fallback is enabled). A warning is displayed in the admin panel reminding you to monitor your API quota.

= Is the telemetry feature active? =

No, by default. The opt-in checkbox is disabled by default. If you enable it, anonymous PHP crash reports (error type, stack trace limited to plugin files, WordPress/PHP/plugin version) are sent to Sentry. No IP address, user data, API keys or personal information is ever transmitted. You can disable it at any time from Preferences → Telemetry.

== Screenshots ==

1. **Bulk optimisation in progress** — Real-time progress bar with speed (img/s), ETA, converted/skipped/errors counter and Pause/Stop controls.
2. **Server Diagnosis** — Green indicators confirm Imagick v3.7, GD, WebP and AVIF are all available. Runs a real encoding probe, not just a format list check.
3. **Preferences** — Output format selector (Automatic / AVIF / WebP / No conversion), auto-convert on upload toggle, and independent quality sliders for WebP and AVIF.
4. **AI Alt Text** — Configure Google Gemini or OpenAI (BYOK), automatic fallback on quota errors, language selector and custom prompt override.
5. **Library Management** — Statistics cards (total images, to convert, disk usage by format, cumulative savings), orphan file scanner and conversion log.

== Changelog ==

= 1.5.6 – 2026-06-25 =
* **Added** — Opt-in data cleanup on uninstall: new "Data & Privacy" section in Preferences with a checkbox to permanently remove all plugin settings and attachment metadata from the database upon uninstallation. Original image files are never deleted.
* **Fixed** — Removed all references to the legacy plugin name from source code, changelog and plugin header.

= 1.5.5 – 2026-06-25 =
* **Changed** — Plugin renamed to "Cetus Image Converter & AI Alt Text" following WordPress.org review.
* **Added** — External Services section in readme.txt documenting Google Gemini, OpenAI and Sentry integrations (data sent, when, Terms and Privacy links) as required by WordPress.org guidelines.
* **Improved** — `register_setting()` callbacks for enum options (`cetus_media_format`, `cetus_media_ai_provider`, `cetus_media_alt_text_language`) now use whitelist validation instead of generic `sanitize_text_field`.

= 1.5.4 – 2026-06-24 =
* **Fixed** — Added "Settings" action link in the plugin row on the Plugins screen for quicker access.
* **Fixed** — PHPCS/WordPress Coding Standards: resolved all remaining errors (translators comments, `imagedestroy()` deprecation notice, empty `elseif`, missing doc comments, `unlink()` → `wp_delete_file()`).
* **Fixed** — PNG alpha channel detection now uses `file_get_contents()` with offset instead of `fopen/fread/fclose`, aligning with WP Filesystem API requirements.
* **Fixed** — `vendor/` directory correctly excluded from Git tracking; Composer dependencies are installed at deploy time by the GitHub Actions workflow.
* **Added** — 5-star review invitation box in the plugin settings page (Preferences tab).

= 1.5.3 – 2026-06-23 =
* **Fixed** — Featured image srcset now correctly regenerated even when the theme omits the `wp-image-{id}` CSS class; attachment ID is read directly from the `post_thumbnail_html` filter parameters.
* **Fixed** — Lightbox `<a href>` is now synced to the optimised format of the wrapped image, preventing the Lightbox from opening the original heavy JPEG.
* **Fixed** — `src`/`srcset` format asymmetry in post content: if `srcset` already contains `.webp`/`.avif` entries but `src` still points to `.jpg`, `src` is promoted to the optimised format.
* **Fixed** — `data-src` (lazy-loading) attributes are now included in the optimisation and rollback pass.

= 1.5.2 – 2026-06-22 =
* **Fixed** — All frontend URL filters now run at maximum priority (`PHP_INT_MAX`) to override theme filters that incorrectly replace image extensions.
* **Fixed** — New `sanitize_optimized_urls` filter on `the_content` and `post_thumbnail_html` detects `.avif`/`.webp` URLs pointing to non-existent files (injected by aggressive theme filters) and rolls back to the original `.jpg`/`.png`, preventing 404 errors, black squares and broken Lightbox links.

= 1.5.1 – 2026-06-22 =
* **Fixed** — GD fallback no longer crashes with a PHP Warning on corrupt or mislabeled image files (e.g. a JPEG renamed `.png`). File validity is now checked with `getimagesize()` before calling GD.
* **Fixed** — Thumbnail size conversion now works correctly in the bulk optimizer on all hosts, including those that store absolute paths in `_wp_attachment_metadata`.
* **Fixed** — Orphan file scanner no longer incorrectly flags registered thumbnail sizes as orphans on hosts with absolute paths in attachment metadata.
* **Fixed** — Bulk optimizer now includes WebP attachments in the queue when the target format is AVIF (WebP → AVIF re-conversion).
* **New** — Plugin version and support links (Docs, Support, GitHub, Email) added to the admin page header.

= 1.5.0 – 2026-06-21 =
* **New** — Progress bar for "Optimize orphan files": tick-based AJAX processing (5 images per call) with real-time progress bar. Fixes timeouts on large libraries.
* **New** — Automatic `AddType image/avif .avif` and `AddType image/webp .webp` written to `.htaccess` on activation and settings save. Fixes AVIF/WebP files downloading instead of displaying on Apache shared hosting.
* **Fixed** — JavaScript broken when other plugins add inline scripts: missing semicolon after `window.CetusMO = {...}` caused `TypeError: is not a function`.
* **Fixed** — PNG images with transparency converted to AVIF produced a black square on Aruba hosting (libheif alpha channel not supported). PNG with alpha now automatically falls back to WebP.
* **Fixed** — PNG images with transparency converted to WebP produced a white background. Now correctly uses `ALPHACHANNEL_ACTIVATE` with transparent background.
* **Fixed** — `cache resources exhausted` on large images: inner try/catch around `mergeImageLayers` frees RAM immediately and falls back to GD. Pre-check skips images over 40 MP. Imagick resource limits set to 256 MB RAM / 512 MB mmap.
* **Fixed** — Batch restart from scratch when nearly complete: `build_queue()` now pre-filters already-converted images.
* **Fixed** — Sentry was capturing errors from third-party plugins. `before_send` now filters to plugin-only stack frames.
* **Fixed** — All ~150 source strings were in Italian instead of English. Now English is the source language; `it_IT`, `es_ES`, `fr_FR`, `de_DE` translations provided.
* **Fixed** — `vendor/composer/installed.php` was excluded from the ZIP causing a PHP fatal on Sentry init.
* **Fixed** — Stop button showed wrong dashicon (rewind instead of stop).

== Upgrade Notice ==

= 1.5.6 =
Adds opt-in data cleanup on uninstall. Enable it in Preferences → Data & Privacy if you want all plugin data removed when deleting the plugin.

= 1.5.5 =
Plugin renamed to 'Cetus Image Converter & AI Alt Text'. Improved sanitization for settings and full External Services documentation added.

= 1.5.4 =
Code quality and stability improvements: PHPCS compliance, Settings shortcut link in plugin row, review invitation box, and Composer dependency management fix.

= 1.5.3 =
Fixes featured image srcset disappearing when the theme omits wp-image-{id} class; syncs Lightbox href and src/srcset to the same optimised format.

= 1.5.2 =
Frontend URL filters now run at maximum priority to neutralise theme interference. Adds automatic rollback to original images when a theme produces broken AVIF/WebP URLs.

= 1.5.1 =
Bug fixes: GD no longer crashes on corrupt image files; thumbnail size conversion now works correctly on all hosts; bulk optimizer now includes WebP attachments for AVIF re-conversion.

= 1.5.0 =
First public release.
