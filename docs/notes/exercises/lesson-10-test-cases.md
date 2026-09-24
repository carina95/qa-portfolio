# Lesson 10 — Executable test cases (learning notes)

The polished cases now live in ../../02-test-cases/functional-test-cases.md.
This file keeps only what I learned writing them.

## Why I split "test search" into two cases
"Make sure results are correct AND the page doesn't break" is two purposes with
two different oracles, so atomicity means two cases: TC-SEARCH-001 (correct
results) and TC-SEARCH-002 (graceful no-match). The "and" in a task is often a
sign of two cases hiding in one.

## Priority reasoning
Highest priority is TC-BSK-001 (add-to-basket): if a user can't add products,
the app fails its core purpose and is unusable. Prioritise by impact × frequency.

## What I corrected after review
- A test case's postcondition must describe THIS test's real end state — I had
  copy-pasted "no user logged in" into a logged-in test.
- Credentials go in the precondition so it's self-sufficient; reaching a
  precondition is setup, so if it fails the test is Blocked, not Failed.
- Copy-paste is the enemy: re-read every field of a duplicated case.