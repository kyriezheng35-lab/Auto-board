---
name: autoboard
description: "Build or improve a public, automatically refreshed information dashboard from traceable web sources. Use for recurring collection, structured briefs, source health, and static deployment; not for one-off research reports."
---

# Autoboard

Build a dependable information pipeline that people can use without the agent, a local computer, or an API key. Optimize for traceability and visible coverage quality rather than pretending the collection is complete.

## Establish the contract first

Before implementing, determine or state the intended public/private access, update cadence and timezone, source authority requirements, regions/topics, retention window, desired languages, and deployment target. Treat user interests as collection criteria unless the user explicitly asks for analysis or recommendations.

Prefer primary sources for policy, regulation, statistics, and official announcements. Record source scope and known exclusions. Do not equate a failed source with “no news.”

## Design the pipeline

Use a source configuration file rather than hard-coding sites. Each source needs a name, canonical URL, geography/topic labels, source type, and category mapping. Support feeds and documented official APIs first; use HTML listing extraction only when necessary.

Normalize records into a stable schema with at least title, canonical URL, source, published date, collection timestamp, labels, extraction method, and a source-verification status. Deduplicate on canonical URL where possible. Keep a time-bounded catalogue and a separate “today” set based on newly discovered or materially changed records.

Generate a machine-readable health report for every run. Capture success, empty result, failure, timing, parsed count, accepted count, and a concise error. Surface that report in the dashboard.

Use rule-based excerpts when no article description is available. Clearly distinguish those from extracted source text, and do not claim full-text comparison or cross-source verification unless it was actually performed.

## Make updates safe and observable

Add syntax checks, parser tests with representative fixtures, and generated-data validation before publishing. Schedule the workflow in an explicit timezone conversion, permit a manual run, and prevent overlapping executions.

When the workflow commits generated data back to Git, limit its write scope to generated files. Synchronize with the remote before push, but stop for a real merge conflict rather than force-pushing or discarding either version. Keep commits granular enough to roll back code independently from generated data.

## Publish safely

Build public assets into a dedicated output directory whenever practical. If a host must publish a repository root, use an explicit ignore list and verify that scripts, CI configuration, secrets, Git metadata, archives, and internal documentation return 404 from the public URL.

Deploy static HTML and data through a Git-connected static host or an artifact deployment workflow. The frontend must use relative URLs and tolerate a missing optional health report so a transient data issue cannot blank the whole page.

Verify after each first deployment or material change: public home page, required data endpoints, browser rendering, and rejection of representative internal paths. Do not claim 24/7 availability from a successful build alone.

## Communicate the result

Report the public URL, cadence, configured source coverage, current health, and remaining limits. Call out degraded sources and distinguish implemented capabilities from planned ones. Ask for authorization immediately before external mutations such as a production deploy, source-account authorization, or enabling paid APIs.

For schemas, checklists, and deployment-specific safeguards, read [implementation checklist](references/implementation-checklist.md).
