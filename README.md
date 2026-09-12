# Skills Ku

A collection of coding-agent skills for isolated implementation workflows and adversarial code review.

## Install

Install interactively from GitHub:

```sh
npx skills add khairilushan/skills-ku
```

Install one skill directly:

```sh
npx skills add khairilushan/skills-ku --skill ku-orchestrator
npx skills add khairilushan/skills-ku --skill ku-review
```

Install both skills for all detected agents:

```sh
npx skills add khairilushan/skills-ku --all
```

Add `--global` to install at the user level instead of the current project.

## Update

The installer records each skill's GitHub source, path, and content hash in `skills-lock.json`. After changes are committed and pushed to this repository's default branch, users can update every installed project skill with:

```sh
npx skills update
```

They can also update one skill or update global installations:

```sh
npx skills update ku-orchestrator
npx skills update ku-review
npx skills update --global
```

Keep the `ku-orchestrator` and `ku-review` names and directory paths stable. Renaming either one requires users to remove and reinstall that skill. Teams using project-level installations should commit their generated `skills-lock.json`.

## Ku Orchestrator

`ku-orchestrator` runs a coding change through seven phases:

1. Resolve the request, plan document, or Jira ticket.
2. Gather repository context with read-only agents.
3. Produce an implementation plan.
4. Send the plan to an independent critic.
5. Assign approved work items to fresh writers.
6. Review the changes and send required fixes to fresh fixers.
7. Check the completed work and report the result.

The coordinator does not edit source files during the pipeline. Writers and fixers make all code changes. Reviewers remain read-only, and agents do not commit or push unless the user requests it after the checks pass.

The skill supports Codex, Claude Code, and other runtimes that can start fresh agents without inherited conversation history.

### Use

Invoke the skill directly with a concrete task:

```text
$ku-orchestrator Add pagination to the audit log endpoint and update its tests.
```

Claude Code can also use the slash command form:

```text
/ku-orchestrator Add pagination to the audit log endpoint and update its tests.
```

The task can be:

- a direct request
- a path or accessible link to a plan document
- one or more Jira ticket keys or URLs
- a clear reference to the plan immediately above the invocation

The skill will stop and ask one question if it cannot access required task material or if a decision would change the implementation.

### Default models

When a runtime accepts a model for each agent, the skill uses these defaults:

| Role | Claude Code | Codex / GPT |
|---|---|---|
| Context agents | `sonnet` | `gpt-5.6-luna` |
| Writers and fixers | `opus` | `gpt-5.6-terra` |
| Planner, plan-reviser, critic, reviewers, and verifiers | `fable` | `gpt-5.6-sol` |

A user can override the model for one role in the request:

```text
/ku-orchestrator Implement this feature and use Astra as the planner model.
```

The override applies only to the named role for that run. Other roles keep their defaults. If the requested model is unavailable, the coordinator records the fallback and uses the session model.

## Ku Review

`ku-review` sends the full review to one fresh isolated adversarial reviewer. The coordinator establishes the comparison scope and returns the reviewer's findings without performing its own review.

### Use

```text
$ku-review Review the current changes.
```

Claude Code can also use:

```text
/ku-review Review the current changes.
```

The review can target uncommitted changes, staged changes, recent commits, another branch, or a pull request target. It inspects code and tests without running builds, tests, linters, apps, or validation commands.

