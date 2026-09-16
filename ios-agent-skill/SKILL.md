---
name: ios-agent-skill
description: Implement and verify SwiftUI iOS features, especially persistence, search, loading and error states, with reproducible Xcode tests and simulator evidence. Use for Swift app work, not unrelated web or backend tasks.
---

# iOS Agent Skill

Adapted from [Nagarjuna Reddy's ios-agent-skill](https://github.com/Nagarjuna2997/ios-agent-skill), MIT licensed.

## When to use

Use when implementing or reviewing an iOS feature in an existing Swift project, or turning an app brief into testable screens and data behavior. Preserve the user's deployment target and existing architecture.

## Plan around observable behavior

Translate the brief into screens, data changes and acceptance checks. For each check, name an executable test or a manual observation. A screenshot proves capture; it does not prove persistence, accessibility or correctness. Keep implementation source separate from the acceptance tests so a repair does not silently weaken the requirements.

Before changing a project, inspect its scheme, dependencies, minimum OS and current tests. If Xcode or a required runtime is unavailable, report that constraint instead of claiming a build passed.

## Swift implementation decisions

- Isolate UI-observed mutable models to `@MainActor`. On supported deployment targets use `@Observable`; inject storage/network services through protocols and initializers.
- Keep storage writes atomic. Update the displayed collection only after a successful save. A load failure must not turn a corrupt on-disk library into an empty successful save.
- Give previews an in-memory service. Keep UI test storage separate from user storage, and restrict reset switches to that test store.
- Model loading, empty, success and error outcomes. Distinguish an empty library from a filtered search with no matches. Preserve cancellation rather than rendering it as an application error.
- Use semantic text styles and colors; give icon-only controls meaningful labels. Verify the requested accessibility behavior rather than promising a complete audit from identifier-based UI tests.

## Build, test, inspect

Run the project's relevant unit tests, then exercise the important simulator journey. For persistence, terminate and relaunch before asserting restored data. For search, test case-insensitive title/author matching and a no-results state. Retain screenshots for important screens and inspect them for clipping and missing content.

When a check fails, classify it as implementation, test setup or environment failure. Use the relevant log excerpt to make a scoped repair and rerun affected checks. Agree a retry bound with the task context; stop with the failing command and reason when the bound is exhausted. Do not claim a passing test from an earlier source revision after making more edits.

Confirm destructive actions and publication unless already authorized by the user. Never publish local credentials, test logs containing private source or `.ios-agent` runtime state.

## Local source and optional MCP tools

The companion repository contains editable samples and Apple guides. When it is checked out, search it with `node scripts/query-library.mjs search "persistence"`, then read a matching file in a bounded section. Do not load its entire technology catalog into every prompt. Apple framework documentation is reference material, not Apple's private framework implementation.

An optional unified stdio connection is available with `npx -y ios-agent-mcp@2.5.1 --project /absolute/path/to/app` (Node.js 20+). Its 34 tools include 11 read-only reviews, 8 reference tools, 14 simulator tools and app scaffolding. Simulator operations require macOS/Xcode; the unified server also exposes write/launch operations. Installing this skill alone does not configure that connection.

The [Reading List sample](https://github.com/Nagarjuna2997/ios-agent-skill/tree/main/samples/ReadingList) includes an Xcode project, storage tests, UI tests and screenshot export. The separate [resumable loop preview](https://github.com/Nagarjuna2997/ios-agent-skill/blob/main/docs/tooling/app-building-loop.md) is source-checkout-only: real Claude planning/repair remains unverified in its development environment because local authentication expired.

## Example

User: "Add a reading list with persistence and search."

Implement title/author entry, a durable store and search states; test failed writes and corrupt loads; verify add, mark-finished, relaunch and search on the simulator. Report the tests actually run, screenshots inspected and unresolved limitations. The reference demo has three unit tests, two UI tests and six captured screen states; adapt the checks to the user's app rather than assuming those counts establish acceptance.
