0. **Always/First-Principle Rules** – apply everywhere, forever. 
1. **SOLID Rules** – apply at every structural boundary (module, class, function). 
2. **KISS Rules** – apply to every line of code and every human interaction. 
3. **Craftsmanship Rules** – apply to how we build, test, ship and operate. 
4. **Spec Rules** – apply to how we capture, refine and verify behaviour.

Each layer **inherits all previous layers** (so “SOLID” must also satisfy “Always Rules”, etc.). 
Nothing that violates a higher-level rule can appear in a lower-level rule.

---

# 0. Always / First-Principle Rules 
*Violating any of these voids the entire change.*

| Rule ID | Statement | Rationale |
|---------|-----------|-----------|
| A0 | **Correctness beats everything else** | If it is wrong, it is useless. |
| A1 | **Minimal moving parts** | Every new concept must pay for itself in reduced total complexity elsewhere. |
| A2 | **Change must be local** | A requirement shift must never ripple further than the module that owns the requirement. |
| A3 | **Tests are the primary truth** | Any behaviour not covered by an automated test does not exist. |
| A4 | **Fast feedback < 60 s** | From “save” to “green/red” must stay under one minute on average hardware. |
| A5 | **Humans are the bottleneck** | Optimise for reading, understanding and modifying code by a stranger at 02:00. |
| A6 | **No dark corners** | Every behaviour must be discoverable in one place: this file or the associated test. |
| A7 | **No tribal knowledge** | Every decision must be expressed as **executable code** or **text in this file**; never in someone’s head. |

---

# 1. SOLID Rules 
*Applies to every structural boundary (module, class, function, package, service).*

| Rule ID | Statement | Checklist |
|---------|-----------|-----------|
| SRP1 | **One reason to change per unit** | If you can describe a unit’s responsibility without using “and” or “or”, it passes. |
| OCP1 | **Closed for direct modification, open for extension** | New behaviour is added via new code, not by editing existing code (except tests). |
| LSP1 | **Subtype must be substitutable** | A failing unit test that passes with the super-type must also pass with every subtype. |
| ISP1 | **No client depends on methods it does not use** | Every interface ≤ 4 methods or has been split. |
| DIP1 | **Dependencies point to abstractions** | A concrete class never depends on another concrete class at compile time. |

---

# 2. KISS Rules 
*Applies to every line, every name, every conversation.*

| Rule ID | Statement | Litmus Test |
|---------|-----------|-------------|
| K0 | **Explain to a junior in < 1 min** | If you cannot, simplify further. |
| K1 | **No abbreviations except domain terms** | Every identifier must read aloud without pause. |
| K2 | **One level of indentation per function** | Nested loops or branches are extracted. |
| K3 | **No magic values** | Every literal beyond 0, 1, empty string, or `true/false` is named. |
| K4 | **Delete > Add** | When in doubt, remove; only merge if CI stays green. |

---

# 3. Craftsmanship Rules 
*Applies to how we build, test, ship and operate.*

| Rule ID | Statement | Concrete Practice |
|---------|-----------|-------------------|
| C0 | **Trunk-Based Development** | All work lives ≤ 24 h in a branch. |
| C1 | **Continuous Integration** | Every push triggers full build + test; red pipeline blocks merge. |
| C2 | **One command env** | `make dev` boots the entire stack in ≤ 5 min on a fresh laptop. |
| C3 | **Observability is built-in** | Every public entry point emits request-id, latency, error and business metric. |
| C4 | **Rollback ≤ 5 min** | Any release can be reverted without data loss or downtime. |
| C5 | **No “works on my machine”** | Reproduce every bug in a containerised test before touching prod. |
| C6 | **Immutable artefacts** | What reaches prod is the exact artefact that passed CI. |
| C7 | **Code is the doc** | Docstrings only explain *why*, never *what* or *how* (that's in code/tests). |
| C8 | **Coach correctness review with critic escalation** | Coach reviews all outputs for correctness, duplicates, and regressions; escalates critical items to critic for deep verification. |

---

# 4. Spec Rules 
*How we capture, refine and verify behaviour.*

### 4.1 Format 
Every behaviour is expressed as **Given/When/Then** in **strict Markdown**:

```markdown
### Spec-ID: USR-42
**Given** an authenticated user 
**When** they request `/account` 
**Then** they receive JSON containing `id`, `email`, `created_at` 
**And** latency ≤ 200 ms at p99 
```

### 4.2 Incremental Lifecycle 
1. **Draft** – written in this file under `## Unapproved`. 
2. **Approved** – moved to `## Approved` only when **two engineers sign** and **an automated test exists**. 
3. **Implemented** – the test passes in CI; code merged to `main`. 
4. **Verified** – 7 days in prod without incident; then tagged `## Verified`. 
5. **Frozen** – after 30 days with no change request; moved to `## Frozen`. 

### 4.3 Test Binding 
- Each `Spec-ID` must map to exactly one test file `<Spec-ID>.test.*`. 
- Test file path mirrors production path; tests live next to code. 
- Test must fail before implementation, pass afterwards, fail again on regression.

### 4.4 Change Process 
- Open PR titled `Spec: <Spec-ID>` that contains: 
  1. Updated markdown snippet in this file. 
  2. Failing test. 
  3. Minimal implementation. 
- Reviewers verify **only**:
  - Rule compliance (A0–C7). 
  - Spec traceability (test ↔ markdown ↔ code). 
- Merge only when CI green and two approvals.

### 4.5 Markdown Anchors 
Every spec carries an anchor `[[Spec-ID]]` so code can link:

```java
// [[USR-42]] latency guard
assertLatency(request, 200);
```

---

# 5. Living Document Mechanics 

### 5.1 Location & Versioning 
- **Single source**: `./spec.md` in repo root. 
- **Versioned** with git; no external wikis, Confluence, or spreadsheets. 
- **Rendered** automatically via CI to `https://docs.<project>.dev/spec`.

### 5.2 Automation Hooks 
- **Pre-commit**: `spec-lint` checks every markdown section has matching test. 
- **CI**: fails if any `## Approved` spec lacks passing test. 
- **Nightly**: diff against prod logs; open PR for any drift.

---

# 6. Glossary 
| Term | Definition |
|------|------------|
| Module | A directory with its own `package.json`, `Cargo.toml`, `go.mod`, etc. |
| Unit | Smallest compilable artefact (function, class, service). |
| Behaviour | Observable externally; anything else is an implementation detail. |

---

# 7. Changelog 
Keep a single **reverse chronological list** at bottom; each entry contains:

```markdown
- 2024-06-10 **USR-42** eample 1 (see commit abc123)
- 2024-06-09 **USR-41** example 2 `legacy_token`
```

---