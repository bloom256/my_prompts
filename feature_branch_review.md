# Project-Specific Context

<!--
Add any project-specific information here when needed, for example:

- Purpose of the feature
- Related ticket or specification
- Important architectural constraints
- Compatibility requirements
- Known limitations
- Files or components that deserve special attention

Leave this section empty when no additional context is required.
-->

# Production Feature-Branch Code Review

Act as the lead reviewer for a pull request intended to be merged into a large production system.

Your goal is not to make the code perfect. Your goal is to identify serious problems that should be fixed before merging, using the smallest practical changes.

The project is urgent. Prioritize production quality, correctness, and safe integration while avoiding speculative improvements, broad refactoring, and low-value feedback.

## Hard constraints

* Do not modify any project files.
* Do not modify implementation code, tests, configuration, formatting, or generated files.
* Do not stage, commit, reset, checkout, rebase, merge, or otherwise change Git state.
* Do not run the application.
* Do not compile or build the project.
* Do not run tests, benchmarks, linters, formatters, static analyzers, package managers, generators, or scripts.
* Do not install anything.
* Do not use network access.
* Only inspect files and Git history.
* The only file you may create or modify is `code_review.md`.
* Subagents must not write files.
* Only the coordinating agent may create or update `code_review.md`.

Read-only commands such as the following are allowed:

* `git status`
* `git branch`
* `git remote`
* `git log`
* `git show`
* `git diff`
* `git merge-base`
* `git rev-list`
* `git blame`
* directory listing, searching, and reading files

Behave as though you are reviewing a pull request diff, not auditing or redesigning the entire repository.

## Step 1: Determine the parent branch

Determine the current feature branch and its most likely parent branch.

Inspect:

* the current branch and its upstream branch;
* remote default branches;
* branches such as `main`, `master`, `develop`, release branches, and other plausible integration branches;
* merge bases and commit ancestry;
* branch-specific repository or CI configuration;
* recent branch history;
* available pull-request metadata.

Prefer an explicit base branch from pull-request metadata or repository configuration when one exists.

Otherwise, infer the parent using Git ancestry.

Use `git merge-base --fork-point` when applicable, with `git merge-base` as a fallback. Prefer the candidate whose common ancestor and divergence most plausibly represent where the feature branch was created.

Do not assume that `main` is the parent.

If several candidates remain plausible, select the strongest candidate based on evidence and mark it as inferred in `code_review.md`.

Review the equivalent of:

`<parent-branch>...HEAD`

Also include staged and unstaged tracked changes when they are part of the feature work.

Exclude `code_review.md` itself from the review.

## Step 2: Understand the project

Before reviewing individual changes, read enough of the repository to understand the system in which the feature will operate.

Inspect relevant sources such as:

* top-level directory structure;
* project README files;
* architecture and design documentation;
* build and dependency configuration;
* major entry points;
* public interfaces;
* core domain models;
* nearby components;
* data flow between affected components;
* error-handling conventions;
* configuration patterns;
* persistence and serialization formats;
* threading or concurrency models;
* platform-specific code;
* deployment or runtime assumptions.

Do not read the entire repository indiscriminately. Focus on the parts needed to understand the changed code and its integration into the existing system.

Build a concise internal model of:

* what the project does;
* which components are affected;
* how those components normally interact;
* what invariants the existing code relies on;
* what production workflows may be affected.

Do not write this internal project summary to `code_review.md` unless it is necessary to explain a serious finding.

## Step 3: Understand the feature branch

Before evaluating the implementation, determine what the feature branch is intended to do.

Use all available local evidence, including:

* the project-specific context section at the beginning of this prompt;
* branch name;
* commit messages;
* changed files;
* added or modified tests, without running them;
* documentation changes;
* comments and configuration changes;
* issue or ticket identifiers mentioned in local files or commits;
* public API changes;
* call sites;
* nearby existing implementations;
* code removed or replaced by the branch;
* differences between the parent branch and `HEAD`.

Inspect both:

* the full feature diff;
* the relevant surrounding code from the parent branch and feature branch.

Do not review isolated diff fragments without understanding their context.

Determine internally:

* the intended user-visible or system-visible behavior;
* the scope of the feature;
* which existing behavior it replaces or extends;
* expected inputs and outputs;
* important edge cases;
* compatibility expectations;
* likely production execution paths;
* the smallest reasonable implementation needed to satisfy the apparent intent.

When the feature intent is ambiguous, infer it from the strongest available repository evidence. Do not invent requirements.

Do not add a finding merely because the implementation differs from an imagined design. Report it only when it conflicts with clear project evidence or creates a concrete production risk.

## Step 4: Read repository-specific instructions

Inspect relevant project guidance, including files such as:

* `AGENTS.md`
* `CLAUDE.md`
* `CONTRIBUTING.md`
* `README.md`
* development documentation;
* architecture documentation;
* language and framework configuration;
* formatter and linter configuration;
* build configuration;
* nearby code implementing similar behavior.

Follow the repository's established architecture, terminology, style, error-handling patterns, compatibility requirements, and language conventions.

Repository-specific rules take precedence over general preferences.

## Step 5: Create independent review agents

Run several independent review agents in parallel when the environment supports subagents.

Before reviewing their assigned area, every agent must independently read enough of the following to understand the feature:

* the project-specific context section;
* relevant repository documentation;
* the complete feature-branch diff;
* feature-branch commit history;
* affected files;
* relevant surrounding implementation;
* comparable existing code;
* important callers and consumers.

Each agent must understand both the purpose of the feature and how it fits into the existing project.

Agents must not inspect only isolated changed lines.

Each agent must review the complete feature diff but focus primarily on one area.

### Agent 1: Correctness and data integrity

Check for serious issues involving:

* incorrect logic;
* behavior that contradicts the apparent feature intent;
* broken edge cases;
* invalid state transitions;
* data loss or corruption;
* race conditions;
* concurrency and synchronization;
* resource lifetime;
* exception and error handling;
* undefined behavior;
* null, bounds, overflow, precision, ownership, and lifetime problems;
* incorrect API usage;
* failure handling and recovery;
* incorrect assumptions about existing project invariants.

### Agent 2: Security and trust boundaries

Check for serious issues involving:

* authentication or authorization;
* unsafe input handling;
* injection;
* path traversal;
* unsafe deserialization;
* secrets or sensitive information;
* privilege boundaries;
* insecure defaults;
* information disclosure;
* cryptographic misuse;
* dependency or configuration changes that create a concrete vulnerability.

Do not report theoretical security concerns without a realistic attack or failure path.

### Agent 3: Architecture and integration

Check for serious issues involving:

* violations of established architecture;
* incorrect component boundaries;
* behavior inconsistent with the feature's intended role;
* API or ABI compatibility;
* serialization and file-format compatibility;
* database or schema compatibility;
* configuration compatibility;
* dependency direction;
* lifecycle and initialization order;
* integration with existing callers;
* upgrade and rollback behavior;
* changes that can break existing production workflows.

Do not propose broad redesigns unless the current implementation creates a concrete production risk.

### Agent 4: Maintainability and readability

Check whether the changed code:

* follows nearby project conventions;
* follows language-specific best practices;
* is understandable in the context of the feature;
* is understandable without unnecessary mental overhead;
* duplicates important logic;
* introduces fragile coupling;
* hides critical behavior;
* is likely to be modified incorrectly later;
* uses misleading names or abstractions that can cause real defects;
* makes the feature significantly harder to maintain than similar existing code.

Report maintainability or readability problems only when they are serious enough to make the code unsafe, highly error-prone, or unreasonably difficult to maintain.

Do not report ordinary style preferences, minor naming issues, formatting, or optional cleanup.

### Agent 5: Production readiness

Check for serious production risks involving:

* major performance regressions;
* unbounded memory, disk, thread, handle, or network usage;
* deadlocks or blocking behavior;
* retry storms;
* missing timeouts where they are required;
* incorrect logging or exposure of sensitive data;
* inability to diagnose important failures;
* unsafe configuration changes;
* platform compatibility;
* backward compatibility;
* deployment, migration, startup, shutdown, or rollback failures;
* behavior that is safe in isolation but unsafe in real production workflows.

Do not request additional logging, metrics, comments, or tests unless their absence makes this specific change unsafe to merge.

### Agent 6: Feature intent and completeness

Check whether the branch actually implements its apparent intended behavior.

Verify:

* all necessary execution paths are updated;
* old and new behavior interact correctly;
* callers and consumers use the new behavior correctly;
* required state is propagated through the system;
* configuration and defaults are connected correctly;
* public interfaces and implementations remain consistent;
* the implementation does not silently handle only part of the intended feature;
* removed behavior is not still required elsewhere;
* fallback and error paths remain valid;
* the implementation does not create a misleading appearance of feature support while missing a critical production path.

Do not report optional enhancements or requirements that cannot be established from repository evidence.

## Step 6: Finding acceptance criteria

An issue may be included only when all of the following are true:

1. It is introduced or materially exposed by the feature-branch changes.
2. It can cause a concrete production failure, security problem, compatibility problem, data problem, serious operational issue, or substantial maintenance risk.
3. It is supported by specific code evidence.
4. It is consistent with the understood project architecture and feature intent.
5. The failure scenario can be explained clearly.
6. A practical minimal correction exists.
7. The finding is not merely a preference, cleanup opportunity, or speculative concern.

Do not include:

* positive feedback;
* compliments;
* general summaries of what was implemented well;
* minor warnings;
* style nits;
* formatting issues;
* optional refactoring;
* micro-optimizations;
* speculative future concerns;
* requests to improve comments or naming without concrete risk;
* findings about unchanged legacy code unless the branch directly relies on, exposes, or breaks it;
* missing tests as a standalone issue unless the changed behavior is critical and cannot reasonably be considered safe without specific coverage;
* duplicated findings;
* findings without a realistic failure scenario;
* imagined requirements unsupported by project or branch evidence;
* alternative designs when the current design is production-safe.

Use only these severities:

* `Critical`: likely security compromise, data loss, corruption, catastrophic production failure, or a change that must not be merged.
* `High`: realistic production failure, serious incorrect behavior, significant compatibility break, or major maintainability hazard that should block the merge.

Do not include medium, low, informational, or nit-level findings.

Prefer high-confidence findings. When evidence is insufficient, omit the issue instead of guessing.

## Step 7: Consolidate the first review

After the independent agents finish:

* combine their findings;
* remove duplicates;
* resolve contradictions using code evidence;
* verify every location against the current diff;
* verify every finding against the understood feature intent;
* remove anything outside the acceptance criteria;
* keep recommendations limited to the smallest safe correction;
* assign stable finding IDs such as `CR-001`, `CR-002`, and so on.

Then overwrite any existing `code_review.md`.

## Step 8: Required `code_review.md` format

Keep the file brief and easy to scan.

Use this structure:

# Code Review

* Parent branch: `<branch>`
* Parent selection: `explicit` or `inferred`
* Reviewed range: `<parent>...HEAD`
* Review method: `static diff review only; code and tests were not executed`
* Merge status: `BLOCKED` or `NO SERIOUS FINDINGS`

## Findings

### CR-001 — High — Short title

* Location: `path/to/file.ext:line`
* Problem: One concise explanation of the defect.
* Risk: The concrete production failure or unsafe behavior.
* Minimal fix: The smallest practical change that removes the risk.

Repeat only for serious findings.

Keep each finding concise.

Include additional locations only when necessary to understand the same issue.

Do not add:

* a positive-feedback section;
* a strengths section;
* minor observations;
* optional suggestions;
* a long executive summary;
* a description of the feature;
* a general project summary;
* detailed explanations of the review process;
* agent names or internal agent discussions.

When no accepted findings remain, use:

# Code Review

* Parent branch: `<branch>`
* Parent selection: `explicit` or `inferred`
* Reviewed range: `<parent>...HEAD`
* Review method: `static diff review only; code and tests were not executed`
* Merge status: `NO SERIOUS FINDINGS`

Do not add praise or additional commentary.

## Step 9: Independent verification iterations

After writing the first version, start a new verification iteration using fresh agents.

Each verification agent must first independently understand:

* the project;
* the purpose of the feature branch;
* the parent branch;
* the complete diff;
* the relevant surrounding code.

The verification agents must not trust either the existing findings or the previous interpretation of the feature automatically.

For every finding in `code_review.md`, independently verify:

* that the cited code exists;
* that it is part of the reviewed feature changes or directly affected by them;
* that the reported behavior is technically correct;
* that the finding is consistent with the actual feature intent;
* that the failure scenario is realistic;
* that the severity is justified;
* that the finding is not duplicated;
* that the suggested fix is minimal and relevant.

The verification agents must also inspect the complete diff for serious findings missed in the previous iteration.

After verification:

* remove false positives;
* remove weak or speculative findings;
* remove findings based on misunderstood feature intent;
* merge duplicates;
* correct inaccurate locations or explanations;
* add newly discovered serious findings;
* reduce severity when necessary;
* remove anything that no longer qualifies as `Critical` or `High`;
* preserve stable finding IDs when the underlying issue remains the same;
* update `code_review.md` only when its substantive content changes.

Then repeat the verification iteration with new agents.

Continue until one complete iteration makes no substantive changes to `code_review.md`.

Changes in wording, ordering, whitespace, or formatting alone do not justify another iteration.

Avoid oscillating between equivalent descriptions. Resolve disagreements using concrete repository evidence.

## Final response

After convergence, respond only with a short statement confirming:

* the detected parent branch;
* whether serious findings remain;
* that `code_review.md` was created or updated.

Do not repeat the findings in the chat response.
