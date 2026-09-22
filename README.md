# Payment Gateway Transaction Flow

An editable, dependency-free HTML/CSS/SVG diagram describing how payment is
blocked while a Pending / Processing payment-gateway transaction is open, and
how each payment gateway's timeout or cancellation behaviour is handled.

## Project Purpose

- Document the transaction flow from participant-initiated payment through
  eligibility checks, PG initiation, monitoring, and success / pending / failed
  outcomes.
- Provide a single reference table of Payment Gateway timeout and cancellation
  behaviour.
- Group payment gateways by the status they can report (Pending, Processing,
  both, or neither).
- Capture the Special Scenarios & MOM Actions, key rules, and a colour legend.
- Keep the whole diagram editable with plain HTML, CSS, and inline SVG — no
  backend, no build step, and no frameworks.

## How to Run / Open the Diagram

No installation or server is required.

1. Open the repository folder.
2. Double-click `index.html`, or open it directly in any modern browser.

Optionally serve it locally:

```bash
# Python 3
python3 -m http.server 8000
# then visit http://localhost:8000
```

## File Structure

```
payment-gateway-flow/
├── index.html      # Main page: layout, content tables, and inline SVG flowchart
├── style.css       # All styling, including the responsive media queries
├── assets/         # Reserved for images or other static assets
└── README.md       # This file
```

> This repository keeps `index.html` and `style.css` at the root so the page
> works by simply opening `index.html`. The `assets/` folder is present and
> ready for any future static files.

## How to Modify the Flowchart

Everything visual lives in `index.html`; colours, sizing, and responsiveness
live in `style.css`.

### Flowchart (SVG)

The flowchart is an inline `<svg viewBox="0 0 780 1420" ...>` inside the
`<section class="center">`. Because it uses a `viewBox`, it scales cleanly with
its container — the drawing coordinates never need to change for screen size.

- **Boxes / outcomes** are `<rect>` elements (`x`, `y`, `width`, `height`, `rx`).
- **Decision nodes** are `<polygon>` diamonds.
- **Arrows** are `<path>` elements using the shared classes defined in `<defs>`:
  - `class="line"` — normal arrow with an arrowhead (`marker-end: url(#arrow)`).
  - `class="edge"` — connector segment drawn *without* an arrowhead, used for
    the long merge bus lines.
- **Labels** are `<text>` elements. Text classes: `title`, `title-sm`, `small`,
  `tiny`, and the coloured `tag` (red, for NO / FAILED) and `tag-y` (green, for
  YES / SUCCESS).

To add a new node:

1. Copy an existing `<rect>` (or `<polygon>`) and adjust its coordinates.
2. Add its `<text>` labels.
3. Connect it with a `<path class="line" ...>` or `<path class="edge" ...>`.
4. Push the `path` data by editing the SVG path mini-language, e.g.
   `d="M530 1124V1170"` means move to (530, 1124) then draw vertically to
   (530, 1170).

Colours used by the SVG are the `fill` / `stroke` values already present on the
shapes, so keep them consistent with the existing nodes and the legend.

### Layout and styling (CSS)

- The three columns are set by `.layout { grid-template-columns: 430px 1fr 410px; }`.
- The SVG fills the centre column via `svg { width: 100%; height: auto; }`.
- Below `1200px` the columns stack (`grid-template-columns: 1fr`) and the SVG
  scales down automatically while its internal connections stay aligned.
- A secondary `600px` breakpoint trims the header type and page gutters only.

## How to Add / Update Payment Gateway Timeout Information

### 1. Update the timeout / cancellation table

Open `index.html` and find the table under
**"Payment Gateway Timeout / Cancellation Reference"**. Each gateway is one row:

```html
<tr><td>1</td><td>Stripe</td><td>1 hour</td><td>Cancel API available</td></tr>
```

Add a row, or edit the **Timeout / Pending Duration** and
**Cancellation / Remarks** cells in place. Renumber the `#` column if you want a
sequential list.

### 2. Update the status grouping

Under **"PG Lists with Status"**, move a gateway name into the matching group:

- `s1` — Only Pending
- `s2` — Only Processing
- `s3` — Both Pending & Processing
- `s4` — Neither Pending nor Processing

Adjust the counts in each `<h3>` and the
`Total Payment Gateways: 24` line in `.total` to match.

### 3. Update the default-timeout rule (if needed)

The default is stated in two places:

- The yellow callout: `<div class="note">…default <b>7-day timeout</b>.</div>`
- The rule list under **"Key Rules / Decision Points"**:
  `<li>If neither exists, use 7-day default timeout.</li>`
- The flowchart node **"Monitor Transaction"** contains the tiny label
  `PG timeout / Cancel API / 7-day default`.

If the default changes, update all three so the documentation stays consistent.

## Deploy to GitHub Pages with GitHub Actions

This repository ships a workflow at `.github/workflows/deploy.yml` that publishes
the static site to GitHub Pages on every push to `master` (or manually).

### One-time setup

1. Push the repository to GitHub (the workflow file must be included).
2. Open the repository on GitHub → **Settings** → **Pages**.
3. Under **Build and deployment** → **Source**, select **GitHub Actions**.
4. Go to the **Actions** tab and either wait for the push trigger or run the
   **Deploy static site to GitHub Pages** workflow via **Run workflow**.

### What the workflow does

- Checks out the repository.
- Configures GitHub Pages.
- Uploads the repo root (`.`) as the Pages artifact — no build step is needed
  because the site is plain HTML/CSS/SVG.
- Deploys the artifact and exposes the live URL as the job output.

The published URL is shown in the workflow run and under **Settings → Pages**,
typically:

```
https://<your-username>.github.io/<repository-name>/
```

### Changing the branch

If your default branch is not `master`, edit the `branches` list under `on.push`
in `.github/workflows/deploy.yml`.

## Notes

- No external dependencies, CDNs, or fonts are used.
- The design, layout, colours, typography, tables, arrows, decision nodes, and
  business logic are preserved from the original source document.
