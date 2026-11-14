# Invalid + Panda Suite Example
- invalid-panda-suite-example
# Kramtone remaster: Invaris setup example

A workspace harness for remastering Kramtone across independent frontend and
backend repositories. [Panda Suite](https://github.com/akkaraponph/panda-suite)
provides engineering workflows;
[Invaris](https://github.com/akkaraponph/invaris) executes the checks defined in
[invaris.json](invaris.json).

## Workspace layout

| Path | Purpose |
| --- | --- |
| [AGENTS.md](AGENTS.md) | Agent instructions, repository boundaries and Panda Suite routing |
| [RTK.md](RTK.md) | RTK command guidance for agents |
| [invaris.json](invaris.json) | Executable verification configuration |
| `kramtone-client/` | [Legacy frontend](https://github.com/akkaraponph/kramtone-client), used as reference |
| `kramtone-service/` | [Legacy backend](https://github.com/akkaraponph/kramtone-service), used as reference |
| `kramtone-client-remaster/` | [Frontend remaster](https://github.com/akkaraponph/kramtone-client-remaster), implementation target |
| `kramtone-service-remaster/` | [Backend remaster](https://github.com/akkaraponph/kramtone-service-remaster), implementation target |

The root [.gitignore](.gitignore) excludes all four application directories from
the harness repository. Each application keeps its own Git history. Ignore rules
apply when this root is a Git repository and do not untrack already tracked files.

## Quick start

Prerequisites: Git, Go 1.23 or newer, and access to the application repositories.
Run commands from the folder containing this README and `invaris.json`.

For a fresh workspace, clone each missing repository into its corresponding
directory. Skip commands for directories that already contain a checkout:

```sh
git clone https://github.com/akkaraponph/kramtone-client.git kramtone-client
git clone https://github.com/akkaraponph/kramtone-service.git kramtone-service
git clone https://github.com/akkaraponph/kramtone-client-remaster.git kramtone-client-remaster
git clone https://github.com/akkaraponph/kramtone-service-remaster.git kramtone-service-remaster
```

Only the two remaster checkouts are required for the current Invaris guards.
Run the pinned Invaris revision used to verify this example:

```sh
go run github.com/akkaraponph/invaris/cmd/invaris@18ecdce15628561654b228a2fa606f985d7b938d check invaris.json
```

Go downloads the pinned module on the first run. If a compatible `invaris` binary
is already installed, the equivalent command is:

```sh
invaris check invaris.json
```

Agents following `RTK.md` should prefix these commands with `rtk proxy` to retain
the complete JSON report. RTK and Panda Suite are not required to run Invaris.

## What the baseline checks

The harness runs four guards: staged and unstaged tracked-file whitespace checks
in each remaster repository. A successful invocation prints a JSON report with
top-level `status: "PASS"` and four passing results.

| Exit code | Meaning |
| --- | --- |
| `0` | Verification succeeded |
| `1` | Verification detected a failure |
| `2` | Execution was inconclusive, refused or misconfigured |

These guards check whitespace in Git diffs. They do not cover untracked files,
application behavior, builds or frontend/backend compatibility. The baseline has
empty `steps` and `challenges` arrays.

If execution fails, confirm both remaster directories are usable Git checkouts
and inspect the failing guard's output. A guard can be run directly, for example:

```sh
git -C kramtone-client-remaster diff --check
```

## Extend the harness

Each guard names an executable and its arguments. For example, the existing
frontend working-tree guard is:

```json
{
  "id": "frontend-working-tree-whitespace",
  "program": "git",
  "args": ["-C", "kramtone-client-remaster", "diff", "--check"]
}
```

Once a remaster repository defines its toolchain, add guards for its actual build,
lint, type, regression and API contract checks. Run each command directly first,
then rerun `check invaris.json`. Use unique guard IDs and explicit argument arrays;
shell syntax requires an explicit shell executable.

Invaris provides a restricted child environment. Add required environment variable
names through a guard's `envAllowlist`; keep secret values outside the harness.
Mutation challenges should target meaningful violations after a corresponding
guard exists. Invaris executes commands with the user's privileges, and its
temporary challenge workspaces are not security sandboxes.

## Work with Panda Suite

Open this root in your agent host so it loads `AGENTS.md`. With Panda Suite
installed, select a relevant skill, for example:

```text
$panda-suite:panda-learn Trace the legacy login flow across kramtone-client
and kramtone-service. Record source paths and behavior for the remaster.

$panda-suite:panda-plan Plan the login remaster across both remaster
repositories, including API contracts, acceptance criteria and verification.
```

Skill names depend on the host's installation; standalone installations may omit
the `panda-suite:` prefix. See the
[Panda Suite installation guide](https://github.com/akkaraponph/panda-suite#install-from-this-checkout)
for setup. Keep application edits and commits in the owning remaster repository,
and report application test results separately from the baseline guards.
