# AutoBoard implementation checklist

Use this reference when creating or materially changing an automated dashboard.

## Suggested data contract

```json
{
  "id": "stable-url-hash-or-provider-id",
  "title": "Official announcement title",
  "url": "https://official.example/item",
  "source": "Official publisher",
  "region": "Configured region",
  "categories": ["policy"],
  "published_at": "2026-10-15",
  "fetched_at": "2026-10-15T08:30:00+08:00",
  "summary": "Extracted description or clearly labelled template",
  "extraction_method": "official-feed | official-json-api | official-html-listing",
  "verification_status": "single-official-source",
  "first_seen": "2026-10-15",
  "last_seen": "2026-10-15"
}
```

Keep the source-health report separate from stories so readers and operators can distinguish an empty day from a failed collector.

```json
{
  "generated_at": "2026-10-15 08:30 (local time)",
  "total_sources": 20,
  "success_sources": 18,
  "degraded_sources": 2,
  "sources": [
    {"source": "Example", "status": "success", "parsed_count": 10, "accepted_count": 7, "duration_ms": 420},
    {"source": "Example 2", "status": "failed", "error": "HTTP 403"}
  ]
}
```

## Collection decisions

- Prefer a documented feed/API over scraping. Preserve URLs and source dates rather than inventing dates.
- An HTML parser should extract dated content links, reject navigation links, and have a fixture that fails if the source layout changes.
- Avoid global title-only deduplication if unrelated sources can publish the same generic heading; canonical URL is the preferred identity.
- Store raw responses only if their retention, licensing, and public/private status are defined. A public dashboard does not automatically make raw source copies appropriate to publish.
- A “changed” record needs a stated comparison target. Title/list-summary fingerprints do not prove that the full source article changed.

## GitHub Actions pattern

The workflow should include: checkout, runtime setup, deterministic dependency installation when needed, syntax/test validation, collection, data validation, and only then a publish or generated-data commit.

- Use `workflow_dispatch` for manual recovery.
- Use a concurrency group so a slow schedule run and manual run cannot write simultaneously.
- If committing generated data, commit only the data paths and pull/rebase before push. Do not use force push as a conflict remedy.
- Treat scheduled runs as best-effort platform jobs: expose last successful generation time and offer a manual run path.

## Static-host hardening

Prefer this layout:

```text
repository/
  site/          # only deployable HTML, JS, CSS, public JSON
  data/          # internal generated state, copied selectively into site/
  scripts/
  .github/
```

If hosting from repository root is unavoidable, use the provider's ignore mechanism and test representative private paths after deployment. Block source code, CI files, VCS directories, archives, unneeded data history, local configuration, and credentials. Never rely on a hidden link as access control.

## Release checks

1. The public home page and each data endpoint return 200 with current content.
2. The page renders if optional health data is temporarily absent.
3. An internal script, a CI workflow path, a VCS path, and an archive path return 404 or equivalent denial.
4. The displayed latest generation time matches the generated data.
5. A deliberately unavailable source appears as degraded rather than silently vanishing.
