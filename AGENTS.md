@RTK.md

# Kramtone remaster workspace

## Objective and repository boundaries

Remaster the existing Kramtone application with Panda Suite for engineering
workflows and Invaris for executable verification. Establish behavior from the
legacy code before changing it; record intentional differences with acceptance
criteria.

This root is the harness repository. The four application directories are
independent Git repositories excluded by the root `.gitignore`. Run application
Git commands with `git -C <repository>` and inspect each affected repository's
status before edits. Read any repository-local `AGENTS.md` before working there.

| Role | Local directory | GitHub repository |
| --- | --- | --- |
| Frontend implementation | `kramtone-client-remaster/` | https://github.com/akkaraponph/kramtone-client-remaster |
| Backend implementation | `kramtone-service-remaster/` | https://github.com/akkaraponph/kramtone-service-remaster |
| Legacy frontend reference | `kramtone-client/` when usable | https://github.com/akkaraponph/kramtone-client |
| Legacy backend reference | `kramtone-service/` when available | https://github.com/akkaraponph/kramtone-service |

Implement application changes in the two remaster repositories. Treat legacy
repositories as reference sources unless the user requests changes there. Verify
the origin URL before committing or publishing. Keep commits scoped to each
repository and identify dependencies between frontend and backend changes.

Inspect the current contents of each checkout before planning work. Fetch missing
source from the linked repositories as needed without overwriting existing local
state.

## Start a task

1. Identify the affected repository and inspect its current source, instructions,
   manifests and Git status. Derive commands from its actual scripts and toolchain.
2. For a migrated feature, trace the legacy UI, API, validation, permissions and
   persistence involved. Record source paths and revisions, expected behavior,
   intentional changes and unresolved decisions.
3. Define the smallest useful slice and observable acceptance criteria. For
   frontend/backend work, agree on request and response shapes, authentication,
   errors and data ownership before implementing both sides.
4. Implement the requested scope and verify it with the affected repository's
   checks. Update relevant contracts and documentation in the same change.

Choose the remaster stack from requirements and recorded decisions. Legacy
dependencies are evidence of the old implementation, not a mandatory new stack.
Use sanitized fixtures and placeholders when carrying over configuration or data.

## Panda Suite routing

Source: https://github.com/akkaraponph/panda-suite

Use the installed `panda-suite:` skills through the host's skill selector. Load
the selected skill's `SKILL.md` and follow its workflow. Standalone installations
may expose the same names without the prefix. Select the smallest relevant set:

| Task | Skill |
| --- | --- |
| Learn legacy behavior | `panda-learn` |
| Clarify remaster requirements | `panda-requirements` |
| Explore design choices or verify capabilities | `panda-brainstorm`, `panda-research` |
| Define API/data contracts or implementation tasks | `panda-spec`, `panda-plan` |
| Implement requested work | `panda-execute` |
| Diagnose failures | `panda-debugger` |
| Verify behavior or review a diff | `panda-test`, `panda-review` |
| Document or prepare a release handoff | `panda-write`, `panda-release` |
| Coordinate work spanning several activities | `panda-lead` |

Use `panda-craft` when explicitly requested to compose selected skills. Skills
can run in one agent; loading several skills does not require delegation. If a
skill is unavailable, report that and use its upstream instructions as reference
without claiming a host invocation or silently installing global tooling.

Keep shared remaster findings, decisions and cross-repository handoffs under
`.panda-suite/` at this root when an artifact is needed. Follow the active skill's
artifact conventions. Keep implementation-specific contracts and operational
documentation in the owning remaster repository. A handoff identifies the
objective, affected repos, source revisions, decisions, artifacts, checks and
remaining work.

## Invaris verification

Source: https://github.com/akkaraponph/invaris

The root `invaris.json` is a bootstrap harness. Its four guards run Git whitespace
checks on staged and unstaged tracked changes in both remaster repositories.
It has no implementation steps or mutation challenges. Passing these guards
establishes whitespace hygiene only; it does not establish application behavior,
coverage of untracked files, integration compatibility or release readiness.

From this root, use an installed compatible Invaris CLI:

```sh
rtk proxy invaris check invaris.json
```

If the CLI is unavailable, Go 1.23+ can run the inspected upstream revision:

```sh
rtk proxy go run github.com/akkaraponph/invaris/cmd/invaris@18ecdce15628561654b228a2fa606f985d7b938d check invaris.json
```

As the targets acquire tooling, add real build, type, lint, regression and contract
guards using explicit programs and argument arrays. Use `envAllowlist` for required
environment names; Invaris child commands otherwise receive a restricted
environment. Confirm commands work in that environment before claiming coverage.
Add a mutation challenge only when a specific guard and meaningful violation can
be tested. Report actual evidence and distinguish legacy output-marker proof
from stronger structured rule evidence.

Treat harness configuration as executable code. Invaris executes local commands
with user privileges; disposable challenge copies are not security sandboxes.
Keep checks local and use isolated test data. Preserve raw failures when RTK's
filtered output omits details needed for diagnosis.

## Completion evidence

Report affected repositories, implemented behavior, commands actually run and
their outcomes, plus any unverified acceptance criteria. Distinguish planned
checks from executed checks and bootstrap guards from application tests. Commit,
push, deployment and publication follow the user's authorization for that task.

Harness sources inspected at initialization (2026-11-14): Invaris
`18ecdce15628561654b228a2fa606f985d7b938d`; Panda Suite
`3c16515e45b1ebc1ea5c2d284d5545ee1136d62f` (README version 1.6.3).
