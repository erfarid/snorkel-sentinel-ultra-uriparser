# Snorkel Sentinel Ultra — uriparser RFC 3986 Review

This repository contains work completed as part of the **Sentinel Ultra** software-engineering task review program associated with **Snorkel AI**. The task focuses on reviewing and improving a C-based bug-fix assignment derived from the open-source `uriparser` project.

## Project Overview

The assignment concerns RFC 3986 URI path normalization. In certain edge cases, removing `.` and `..` path segments can incorrectly remove a required trailing slash when an absolute URI collapses to its root.

Example:

```text
Input:    http://a/b/c/../../..
Expected: http://a/
