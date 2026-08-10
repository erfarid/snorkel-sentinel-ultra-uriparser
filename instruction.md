Dot-segment removal can drop a required trailing slash when a URI path collapses to its root. This affects path normalization and relative-reference resolution.

For example, resolving `../..` against `http://a/b/c/../d;p?q` must produce `http://a/`, and normalizing `http://a/b/c/../../..` with `URI_NORMALIZE_PATH` must also produce `http://a/`.

Correct dot-segment handling for the ANSI and Unicode APIs so that:

- Absolute paths that collapse to the root retain `/`.
- Relative paths that collapse completely remain empty and do not gain a leading slash.
- Unresolved traversal in relative paths is preserved. For example, `../b/d` remains unchanged, and `a/../../b` normalizes to `../b`.
- A leading `./` is preserved when removing it would change the meaning of the reference, such as `./http://foo`.
- Existing normalization and reference-resolution behavior remains unchanged.

The following examples define required behavior:

| Operation | Input | Expected result |
| --- | --- | --- |
| Normalize path | `http://a/b/c/../../..` | `http://a/` |
| Normalize path | `http://a/b/../c/../..` | `http://a/` |
| Normalize path | `http://a/..` | `http://a/` |
| Normalize path | `/..` | `/` |
| Normalize path | `http://a/..///` | `http://a///` |
| Normalize path | `http://a/..///..` | `http://a//` |
| Normalize path | `a/b/c/../../..` | empty path |
| Normalize path | `a/b/../../c/..` | empty path |
| Resolve `../..` | Base `http://a/b/c/../d;p?q` | `http://a/` |
| Resolve `../../..` | Base `http://a/b/c/d;p?q` | `http://a/` |

Do not change the public API or modify test files.
