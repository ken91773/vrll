# VRLL Website Handoff (vrllsim.com)

Marketing/landing site for **VRLL — Helicopter Longline Simulator**. This is the
website repo, **separate from the Unity game project** (that lives at
`C:\Users\USER\Documents\vrll\VRLL1`).

## Hosting & repo

- **Repo:** `ken91773/vrll` (GitHub), branch `main`.
- **Local clone:** `C:\Users\USER\Documents\vrll\vrll-site`
- **Hosting:** GitHub Pages (legacy build, serves `/` on `main`).
- **Domain:** `vrllsim.com` via the repo `CNAME` file. DNS is correct
  (apex A records → 185.199.108–111.153; `www` CNAME → `ken91773.github.io`).
- **Deploy:** just `git push origin main`; Pages rebuilds in ~1–2 min.
- **Git identity:** repo has no committed user config; commit with inline identity
  matching prior commits:
  `git -c user.name="Ken Kuwahara" -c user.email="ken91773@users.noreply.github.com" commit ...`

## Files

| File | Purpose |
|------|---------|
| `index.html` | Single-page landing site (all CSS inline in `<style>`). Hero, About, Features, Video (placeholder), Status/roadmap, Support/PayPal, Footer. |
| `about-ken.html` | Bio page for Ken Kuwahara ("Why VRLL" card, longline experience). |
| `contact.html` | VRLL-styled inquiry page embedding the Google Form, with a direct-form fallback link. |
| `assets/vrll-bg.jpg` | Hero background (currently from `vrllbg5.png`, 2048×1144, ~414 KB). |
| `assets/vrll-title.png` | Hero title graphic "Helicopter Longline SIMULATOR" (from `titlevrll3.png`, trimmed+resized to 1240×347). |
| `assets/vrll-logo.png` | Logo (top-left of hero; contains small "Helicopter Longline Simulator" subtext). |
| `assets/ken-*.jpg` | Photos on About Ken page. |
| `CNAME` | `vrllsim.com` (do not delete). |

## Current hero layout (index.html)

- `.hero` background: `url('assets/vrll-bg.jpg?v=4') left center / cover` — **left-anchored**
  so subjects (helipad/logo) aren't cropped on tall/narrow viewports. Top padding
  reduced to 28px.
- `.hero-top` flex row: **logo left**, **title graphic right** (`assets/vrll-title.png?v=3`).
- Badges: Windows logo badge (no free-beta badge).
- `.cta-row`: yellow **Become a beta tester** button (subtext **Join our Discord for latest updates**,
  links to Discord invite) + **System Requirements** hover badge.
- **System Requirements** is a `.sys-req-badge` (gear pill). Hovering (or focus)
  reveals `.sys-req` popover card (340px, with arrow). No longer an always-on card.

## Current product messaging

- References to the product being free are qualified as applying to the current
  beta version; do not imply that future releases will always be free.
- The "Who it's for" section includes experienced simulator pilots who want to
  practice advanced real-world concepts such as vertical-reference technique,
  hover precision, swing management, and load placement.
- "Built for helicopter pilots by an experienced VRLL pilot." continues directly
  in the preceding paragraph without a paragraph break.
- "Free Play" remains unchanged because it is the name of a gameplay mode, not a
  pricing statement.

## Inquiry form

- Inquiry links on `index.html` and `about-ken.html` point to `contact.html`.
- `contact.html` embeds the Google Form using its `?embedded=true` URL and provides
  a direct Google Forms link if the embed does not display.
- Form URL:
  `https://docs.google.com/forms/d/e/1FAIpQLSfIP2Gi2Dzd_ekmIArTsYFFi3K1DJ-K7JHo30_IbU0hdK5Ijw/viewform`

## Image workflow (important)

New background/title images arrive in `C:\Users\USER\Downloads\`. Process before use:
- **Backgrounds:** convert PNG → optimized JPG (quality 85) as `assets/vrll-bg.jpg`,
  keep same filename, bump `?v=N` cache-bust in the CSS `url(...)`.
- **Title/logo (need transparency):** keep PNG. **Trim transparent margins**
  (alpha-based bounding box) and downscale to ~2× display width (~1240px),
  bump `?v=N` on the `<img>` src.
- Cache-busting matters: same filenames + GitHub Pages `max-age=600` means browsers
  cache old assets; always bump the `?v=` query. Tell user to hard-refresh (Ctrl+F5).
- Local preview: headless Edge screenshot to verify before pushing:
  `& "C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe" --headless --disable-gpu --hide-scrollbars --user-data-dir="$env:TEMP\edge_fresh_<rand>" --window-size=1280,760 --screenshot="out.png" "file:///.../index.html"`
  (use a fresh `--user-data-dir` to avoid Edge caching old assets). Delete temp files after.

## OPEN ITEM — HTTPS certificate (in progress)

- **Symptom:** browsers warned "not secure" / cert mismatch on vrllsim.com.
- **Cause:** GitHub Pages was serving its default `*.github.io` cert (doesn't cover
  vrllsim.com) because the Let's Encrypt cert for the custom domain hadn't finished
  provisioning. DNS is correct — it was a provisioning delay stuck at state
  `authorization_created`.
- **Action taken (Jul 8 2026 ~1:05pm PT):** re-triggered issuance via API by clearing
  and re-adding the custom domain:
  ```
  '{"cname":null}'          | gh api --method PUT repos/ken91773/vrll/pages --input -
  # wait ~10s
  '{"cname":"vrllsim.com"}' | gh api --method PUT repos/ken91773/vrll/pages --input -
  ```
  CNAME file confirmed intact after.
- **Next step:** check cert state; when it reaches `approved`, enable Enforce HTTPS:
  ```
  gh api repos/ken91773/vrll/pages   # look at .https_certificate.state
  # once "approved":
  '{"https_enforced":true}' | gh api --method PUT repos/ken91773/vrll/pages --input -
  ```
  Provisioning typically completes 15–60 min after DNS is valid (up to 24h). If it
  stalls >24h, repeat the clear/re-add nudge.

## Conventions

- Keep edits minimal and match existing inline-CSS patterns and naming.
- Only commit/push website changes when the user asks (they generally do here).
- Always bump `?v=` cache-bust when replacing an asset under the same filename.
- Preview with headless Edge before pushing when layout/images change.
