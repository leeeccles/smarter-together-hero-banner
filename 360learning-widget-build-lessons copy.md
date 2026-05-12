# 360Learning Widget Build Playbook

This playbook captures reusable lessons for building custom widgets that live
inside 360Learning pages. It focuses on sizing, iframe behaviour, visual design,
data safety, refresh patterns, and the GitHub/Vercel workflow.

It deliberately avoids prescribing a specific layout. Future widgets may be
dashboards, banners, explainers, forms, calculators, scorecards, carousels, or
other experiences.

## Core Principle

Design the widget as part of the 360Learning page, not as a separate app pasted
into it.

The widget should feel:

- Clear.
- Lightweight.
- Purposeful.
- Easy to scan.
- Native enough to sit comfortably inside the platform.

## Recommended Embed Pattern

For interactive or API-powered widgets, host the experience externally and embed
it with an iframe:

```html
<iframe
  src="https://your-vercel-project.vercel.app/"
  style="width:100%; height:620px; border:0; border-radius:8px;"
  loading="lazy">
</iframe>
```

Use `620px` as a strong starting height for substantial dashboard-style widgets.
For smaller banners or simpler content, use less. For more complex tools, test
the actual page and choose the smallest height that avoids clipping.

Useful height guidance:

- `180-260px`: simple banner, announcement, or CTA.
- `320-440px`: compact explainer, status card, or small interactive block.
- `560-640px`: richer dashboard or multi-section widget.
- Above `640px`: use with care, because it can dominate the group page.

If `620px` works in the real 360Learning page, keep it. Extra height creates
unnecessary blank space and can push native page content too far down.

## 360Learning HTML Widget Constraints

- HTML widgets appear in the web app, not the mobile apps.
- Native widget snippets can include HTML and CSS, but script support is
  restricted.
- For JavaScript, API calls, or more complex interactivity, use a hosted page in
  an iframe.
- The iframe source must use HTTPS.
- CSS inside the hosted page is isolated from the 360Learning platform.
- Platform CSS will not rescue spacing, fonts, or responsiveness inside the
  iframe.
- Keep the pasted 360Learning widget code minimal. Ideally it is only the
  iframe.

## Sizing Lessons

### Design For The Real Content Column

Do not design only in a full-width browser preview.

360Learning group pages may include:

- A left navigation rail.
- A page header.
- Tabs or filters above the group content.
- A right social/activity panel.
- Scrollbars and page layout padding.

This means the actual widget width can be much narrower than a desktop browser.

Always test:

- Full local browser width.
- The real 360Learning page.
- A smaller laptop viewport with the right panel visible.

### Fixed Height Is Useful But Brittle

Fixed-height iframes are predictable, which is useful for page layout. The trade
off is that internal content can clip if text wraps or data grows.

Good practice:

- Set a clear CSS height inside the hosted page.
- Match the iframe height to that CSS height.
- Keep copy concise.
- Avoid long headings inside compact cards.
- Avoid relying on content-driven height unless the iframe is allowed to grow.
- Use `overflow: hidden` only when the number of elements is controlled.
- If data can grow, decide whether to show top N items, paginate, or use an
  internal scroll area.

Example:

```css
:root {
  --widget-height: 620px;
}

body {
  min-height: var(--widget-height);
}

.widget {
  height: var(--widget-height);
  overflow: hidden;
}
```

### Use Consistent Spacing

The widget feels more polished when the spacing system is consistent.

Good rules:

- Use one shared gap token for major sections.
- Match horizontal and vertical gaps when building grids.
- Tighten spacing when shadows are removed, because flat cards need less air.
- Do not let cards touch unless the design intentionally creates one combined
  surface.

In this build, a `12px` major gap felt better than `16px` once the widget moved
to flat bordered cards.

### Responsive Breakpoints Should Match 360Learning

Common device breakpoints are not enough. Add breakpoints for the actual embed
column width.

For example, an intermediate breakpoint around `1100-1200px` can be useful
because that may be where the widget starts feeling cramped inside the
360Learning page even though it is not a tablet-sized viewport.

Useful CSS habits:

```css
.card,
.panel,
.grid-child {
  min-width: 0;
}

@media (max-width: 1180px) {
  .compact-card {
    grid-template-columns: minmax(0, 1fr);
    grid-template-rows: max-content max-content;
    align-content: center;
    align-items: start;
  }
}
```

## Layout Considerations

Do not start with decoration. Start with the job of the widget.

Ask:

- What is the one thing this widget must help someone understand or do?
- Is it mainly informational, motivational, operational, or interactive?
- Does it need a CTA?
- Does it need live data?
- Does it need comparison between groups or items?
- How much surrounding 360Learning content will be visible?

Use the simplest structure that answers the job.

Common patterns:

- One-card announcement.
- Two-card status and action block.
- Metric strip plus detail panel.
- Explainer with CTA.
- Compact dashboard.
- Embedded external tool or form.

Avoid assuming every widget needs:

- A leaderboard.
- Multiple cards.
- A chart.
- A hero section.
- A big title.
- A CTA.

## Visual Design Lessons

### Prefer Flat Surfaces Inside 360Learning

Heavy shadows can make an iframe feel like a separate app floating inside the
platform.

Better defaults:

- Transparent page background.
- Light card borders.
- Minimal shadows or no shadows.
- Small border radius, commonly around `8px`.
- Strong contrast only where it helps hierarchy.

### Remove Redundant Elements

The cleanest version usually comes from removing things.

Common redundancies:

- An icon that repeats the CTA.
- An eyebrow label that repeats the heading.
- A callout that repeats the first row of data.
- Decorative row highlights when rank/labels already communicate status.
- Long copy that restates what the chart or metric already says.

Useful test:

```text
If this element disappeared, would the widget become less useful?
```

If not, remove it.

### Make One Or Two Things Visually Important

Do not make every surface loud.

Choose the visual anchor based on the widget's purpose:

- Primary metric.
- Main CTA.
- Alert/status.
- Key content item.
- Interactive control.

Everything else should support that anchor.

### Match The Platform Context

360Learning pages are functional, content-rich, and relatively restrained. A
widget can be polished and branded, but it should not fight the surrounding UI.

Avoid:

- Excessive shadows.
- Overly game-like treatment unless the use case truly needs it.
- Large decorative backgrounds that reduce readability.
- Too many colours competing at once.
- Marketing-page hero styling inside a functional group page.

## Copy Lessons

Short copy almost always works better in an iframe.

Good widget copy:

- States the action or insight plainly.
- Uses the audience's language.
- Avoids internal platform jargon unless users already know it.
- Avoids explaining the whole system.
- Avoids repeating nearby labels or visualizations.

Use the smallest amount of text that helps the user act.

Before:

```text
Complete this task to help your team climb the leaderboard and stay on top of
your assigned work.
```

After:

```text
They will appear in My Work on the homepage when assigned.
```

## Buttons And CTAs

Use a CTA only when the widget points to an action.

Recommendations:

- One primary CTA per compact widget section.
- Keep button text short.
- Align the CTA with the related text at narrower widths.
- Do not add an icon unless it improves recognition.
- Avoid multiple competing buttons in a small iframe.

For responsive layouts, define explicit grid rows so the button does not drift
out of alignment:

```css
@media (max-width: 1180px) {
  .info-card {
    grid-template-columns: minmax(0, 1fr);
    grid-template-rows: max-content max-content;
    row-gap: 18px;
  }

  .info-card a {
    grid-row: 2;
    justify-self: start;
  }
}
```

## Data Safety Lessons

For API-powered widgets, assume the iframe browser is public to the user.

Do:

- Keep API credentials server-side.
- Aggregate data on the server.
- Return only safe summary JSON.
- Avoid exposing names, emails, user IDs, or raw records unless the use case
  explicitly requires it and the audience is authorized.
- Use environment variables for credentials.
- Keep `.env` ignored by Git.

Do not:

- Put API credentials in frontend JavaScript.
- Query sensitive APIs directly from the browser.
- Return raw API responses when only totals are needed.

## API And Refresh Lessons

### Use A Serverless Summary Endpoint

A useful pattern is:

```text
/api/summary
```

That endpoint should:

- Read credentials from environment variables.
- Fetch 360Learning data server-side.
- Aggregate and sanitize.
- Return a compact payload to the browser.
- Fall back gracefully if live data fails.

### Cache The Endpoint

Use caching to reduce API load:

```text
cache-control: s-maxage=300, stale-while-revalidate=3600
```

This lets Vercel cache for 5 minutes and serve stale data while refreshing in
the background.

### Prefer Quiet Auto-Refresh

For live-ish widgets, quiet refresh is usually better than a refresh button.

Recommended behaviour:

- Load data when the iframe loads.
- Refresh every 5 minutes for dashboards or status widgets.
- Do not overlap requests.
- If a background refresh fails, keep the existing UI visible.
- Pause refresh while the tab is hidden.
- Refresh when visible again if the data is old.

For static or low-change content, load-only refresh is fine.

## GitHub Workflow

Recommended process:

1. Create a repository for the widget.
2. Commit the working local version.
3. Push to `main`.
4. Keep `.env` out of Git.
5. Add `.env.example` with safe placeholder names.
6. Commit in small milestones.
7. Use clear commit messages.

Before pushing:

```sh
git status
node --test
node --check public/app.js
```

## Vercel Workflow

1. Import the GitHub repository into Vercel.
2. Confirm the project root.
3. Use default/static settings unless the app needs a build step.
4. Add environment variables in Vercel.
5. Deploy.
6. Confirm the live page loads.
7. Confirm any API endpoints return `200`.
8. Confirm the deployed HTML references the latest asset version.
9. Paste the Vercel URL into the 360Learning iframe.

Useful environment variable naming pattern:

```text
THREE_SIXTY_LEARNING_BASE_URL
THREE_SIXTY_LEARNING_CLIENT_ID
THREE_SIXTY_LEARNING_CLIENT_SECRET
```

Add use-case-specific variables as needed, such as field names, IDs, or feature
flags.

## Verification Checklist

Before calling a 360Learning widget finished:

- It works in local preview.
- It works in the real 360Learning page.
- It fits the chosen iframe height.
- It does not clip on smaller laptop widths.
- Major spacing feels balanced.
- Copy is short and clear.
- Redundant decorative elements have been removed.
- CTA alignment works at narrow embed widths.
- API credentials are server-side only.
- Browser payload contains only appropriate data.
- Live Vercel URL loads.
- API endpoint returns `200`.
- Caching and refresh behaviour are intentional.
- GitHub repo is clean and pushed.

## Reusable Starting Defaults

For future widgets, these are good defaults to begin with:

- Hosted page on Vercel.
- iframe embed in 360Learning.
- Transparent hosted page background.
- Light bordered cards.
- `8px` border radius.
- `12px` major grid gap.
- `620px` height for richer dashboard-style widgets.
- Short copy.
- Server-side data aggregation.
- Cached summary endpoint.
- Quiet 5-minute auto-refresh only when useful.

