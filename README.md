# Snorkel Sentinel Ultra — uriparser RFC 3986 Review

This repository contains work completed as part of the **Sentinel Ultra** software-engineering task review program associated with **Snorkel AI**. It focuses on reviewing and improving a C-based bug-fix task derived from the open-source [`uriparser`](https://github.com/uriparser/uriparser) project.

## Project Overview

The task concerns URI path normalization according to **RFC 3986**. In certain edge cases, removing `.` and `..` path segments can incorrectly remove a required trailing slash when an absolute URI collapses to its root.

For example:

```text
Input:    http://a/b/c/../../..
Expected: http://a/
```

The task covers:

- Absolute and relative URI paths
- RFC 3986 dot-segment removal
- Trailing-slash preservation
- URI reference resolution
- ANSI and Unicode APIs
- Regression testing for existing behavior

## The Problem

In URI paths:

- `.` represents the current directory.
- `..` represents the parent directory.

For example:

```text
/a/b/../c
```

should normalize to:

```text
/a/c
```

The original implementation handled most cases correctly but could lose the final `/` when an absolute path collapsed to its root.

Incorrect result:

```text
http://a
```

Correct result:

```text
http://a/
```

Relative paths require different handling. When every segment in a relative path is removed, the result should be an empty path rather than `/`.

Example:

```text
Input:    a/b/c/../../..
Expected: ""
```

## Expected Behaviour

The implementation must ensure that:

- Absolute paths collapsing to the root retain `/`.
- Relative paths collapsing completely remain empty.
- Relative paths do not gain an unnecessary leading slash.
- Unresolved `..` traversal is preserved.
- A leading `./` is preserved when removing it would change the URI’s meaning.
- Existing correct normalization and reference-resolution behaviour remains unchanged.
- ANSI and Unicode APIs produce equivalent results.

## Sentinel Ultra Review Work

The task package was reviewed according to the **Sentinel Ultra Contributor Guidelines**.

The original task was classified as **Fixable** because its instructions, tests, and execution environment required improvements.

The revision included:

- Rewriting the instructions to focus on observable behaviour.
- Removing internal implementation details and solution hints.
- Expanding the verifier from 2 to 12 fail-to-pass tests.
- Adding 6 pass-to-pass regression tests.
- Covering both ANSI and Unicode API variants.
- Updating the Harbor agent timeout.
- Verifying the required network configuration.
- Removing unnecessary `curl` packages to avoid a known Harbor runner issue.
- Keeping the original base repository unchanged.
- Preserving the original oracle implementation.

## Test Coverage

The revised tests cover:

- Absolute paths collapsing to the root
- Relative paths collapsing to an empty path
- URI normalization
- Relative-reference resolution
- Trailing-slash preservation
- Unresolved parent traversal
- Meaning-preserving leading `./`
- ANSI API behaviour
- Unicode API behaviour
- Previously correct regression cases

The task includes:

```text
12 fail-to-pass tests
6 pass-to-pass regression tests
```

## Validation

The following checks were performed:

- Verified the ZIP structure and integrity.
- Confirmed that task files are stored directly at the archive root.
- Confirmed that no `runs/` or `task/` directory is included.
- Verified that `instruction.md` and `environment/problem_statement.md` match.
- Verified that `tests.patch` applies cleanly.
- Verified that the oracle patch applies cleanly.
- Compiled the C library using GCC.
- Confirmed that the original implementation fails all 12 new bug tests.
- Confirmed that the oracle implementation passes all 12 targeted bug tests.
- Verified the Harbor network configuration.
- Increased the agent timeout to 7200 seconds.
- Removed unnecessary `curl` dependencies.

## Task Structure

```text
.
├── environment/
│   ├── Dockerfile
│   ├── problem_statement.md
│   └── repo/
├── instruction.md
├── solution/
│   ├── golden.patch
│   └── solve.sh
├── tests/
│   ├── config.json
│   ├── tests.patch
│   └── test.sh
└── task.toml
```

## Technologies Used

- C
- C++
- CMake
- GoogleTest
- Docker
- Harbor task format
- RFC 3986
- Git
- GCC

## Repository Visibility

This repository should remain **private** if it includes:

- Sentinel Ultra task packages
- Evaluation tests
- Oracle solutions
- Internal instructions
- Non-public Snorkel AI project materials

Do not publish confidential or proprietary task materials without permission.

## About Snorkel AI

[Snorkel AI](https://snorkel.ai/) develops data-centric artificial intelligence technologies and tools for building, managing, and improving machine-learning datasets and systems.

Sentinel Ultra is a software-engineering task review initiative associated with Snorkel AI. It focuses on creating clear, realistic, verifiable, and technically challenging coding tasks for evaluating advanced coding agents.

## Disclaimer

This is an independent contributor repository created for task-review, educational, and portfolio purposes.

It is not an official Snorkel AI repository or product, and no endorsement by Snorkel AI is implied. Snorkel AI, Sentinel Ultra, `uriparser`, and all other product or project names belong to their respective owners.

## License

The original `uriparser` source code remains subject to its original open-source license. Any additional review notes or supporting materials in this repository should only be reused in accordance with their applicable terms and project confidentiality requirements.
