# QA Analysis and Test Case Design Prompts

This repository contains prompts for three QA tasks that come up in every sprint:

- Reviewing a user story before it is marked Ready.
- Writing test cases for a single ticket.
- Building a traceable test suite across several tickets.

Each prompt produces the same output structure every time you run it. Every repository file listed below comes with a sample output.

The prompts work in any general-purpose AI assistant. They also work as a manual checklist if you prefer to do the analysis yourself.

## Repository contents

| Path | Description |
| --- | --- |
| `prompts/01-requirement-analysis.md` | Refinement/LOE gate review for one or more user stories |
| `prompts/02-test-case-design.md` | Test case design for a single ticket |
| `prompts/03-rtm-test-suite.md` | RTM and test suite for multiple tickets |
| `examples/AI_Requirement_Analysis.docx` | Sample gate review of five stories |
| `examples/QA_Test_Case_Design_JIRA-4521.docx` | Sample test design for one ticket |
| `examples/QA_Test_Case_Suite.docx` | Sample RTM-based suite covering three tickets (31 test cases) |

All tickets and stories in the examples are fabricated. They are not taken from any real product or project.

## Prompts

### 1. Requirement analysis (Refinement/LOE gate)

**When to use:** during backlog refinement, before a story is committed to a sprint.

**What it checks:**
- Whether the acceptance criteria actually cover the story's stated scope.
- What would stop QA from testing it: test accounts, environment access, devices, and unfinished dependencies.
- An effort estimate split across three QA engineers.

**Output:**
- A gap analysis.
- A blockers table.
- An effort estimate.
- RTM-style test cases.
- A business impact note.
- A Ready or Blocked verdict.

The verdict uses a stated threshold. The default threshold is:
- no open blocking dependencies,
- at least 80% AC-to-test-case traceability, and
- zero unresolved S1 gaps.

```text
As QA lead, review this User Story and its draft Acceptance Criteria BEFORE it
is marked Ready for Sprint (Refinement/LOE gate).

1. AC Gap Analysis — Do not assume AC is complete. Flag missing scenarios,
   requirement files (XDS, Figma, etc.), ambiguities, and edge cases against
   the story's own stated scope (not just the drafted AC).
2. Testability Blockers — Test accounts, environment/tool access (New
   Relic/Kibana/Raptor/etc.), device/platform coverage (state exact count of
   platforms/devices), and status of any linked dependencies
   (3rd-party/Head-End/Back-End/API) (must be Done to finalize estimate).
3. Testing Scope & Type — Functional, regression, integration, UI/visual,
   analytics/data validation, etc.
4. Effort for 3 QA persons — Assume mix, parallel work, split rationale stated.
5. Test Cases — RTM format: [AC Ref | TC ID | Technique (EP/BVA/Decision
   Table/State Transition/Error Guessing/Checklist Based) | Steps | Expected
   Result | Severity S1-S2 (S1=blocks release, S2=major)]. One TC per existing
   AC scenario (happy path) and Error Handling/Accessibility/Reporting if
   available (N/A if not). PLUS additional TCs (state type of testcase:
   negative/edge cases/etc.) for every gap identified in step 1, explicitly
   tagged "GAP — not in AC."
6. Business Impact — Value of testing this + risk of NOT testing adequately.
7. Ready/Blocked Verdict — Explicit, with a stated threshold (e.g., no open
   blocking dependencies AND ≥80% AC-to-TC traceability AND zero unresolved
   S1 gaps).

User Story:
[PASTE STORY TITLE, DESCRIPTION, DRAFT AC, LINKED ARTIFACTS (Figma/XDS/API
docs), DEPENDENCY STATUSES, TARGET PLATFORMS HERE]
```

### 2. Test case design (single ticket)

**When to use:** after a story is Ready and you need detailed, executable test cases.

**What it produces:** each test case gets a header and a step table. The prompt requires one test case per AC scenario. It also requires separate test cases for:
- Cross-Client
- Error Handling
- Accessibility
- Reporting
- each gap it finds

```text
As senior qa, design testcase for this ticket with this requirement:

[requirement]

Each test case header must include:
- TC ID + short title
- AC Ref — Scenario N (state the acceptance criteria for each scenario, with
  only 1 testcase per scenario) / Cross-Client / Error Handling (N/A if
  unavailable) / Accessibility (N/A if unavailable) / Reporting (N/A if
  unavailable) / "GAP — not in AC" (each in its own separate test case)
- Type — Happy Path, Negative, Edge Case, etc.
- Technique — EP / BVA / Decision Table / State Transition / Error Guessing /
  Checklist Based (if a GAP case: state which technique surfaced the gap, and
  why)
- Severity baseline — S1 = Critical (functionality), S2 = Major (cosmetic, etc.)
- Preconditions / test data
- Step table — Step # | Action | Expected Outcome | Severity (S1–S2)
```

### 3. RTM-based test suite (multiple tickets)

**When to use:** when you need traceability across a set of tickets, for example for a release or a sprint.

**What it produces:**
- An RTM for each ticket.
- The test cases derived from that RTM.
- A coverage summary.

```text
As Senior QA, generate an RTM for each ticket

[list of requirements]

Generate a test case suite.

Each test case header must include:
- TC ID + short title
- AC Ref (Scenario N (state full acceptance criteria for each scenario) /
  Cross-Client / Error Handling / (N/A if unavailable) / Accessibility (N/A if
  unavailable) / Reporting (N/A if unavailable) / "GAP — not in AC"). Each in
  different testcases
- Type: Happy Path, Negative, Edge Cases, etc
- Technique (EP / BVA / Decision Table / State Transition / Error Guessing /
  Checklist Based — state which and, if a GAP case, why that technique
  surfaced the gap)
- Severity baseline = S1 = Critical (Functionality etc), S2 = Major
  (Cosmetic, etc)
- Preconditions / test data
- Each step row must include: Step # | Action | Expected Outcome | Severity
  (S1-S2).
```

## How to use

1. Copy the prompt that matches your task.
2. Replace the bracketed placeholder with your ticket content. Include the full acceptance criteria and any notes that sit outside the AC, such as field limits or performance targets. Gap cases usually come from those notes.
3. Run the prompt and review the output before you use it. See [Limitations](#limitations).
4. Paste the result into your test management tool or document template.

The output quality depends on what you give the prompt. A ticket with only a title and one line of AC will produce thin test cases and a long list of gaps. That list is useful in its own right, because it tells you what to take back to the Product Owner or BA.

## Conventions

The three prompts use the same terms and scales, so their outputs can be read side by side.

**Severity**

| Level | Meaning |
| --- | --- |
| S1 | Critical. Core functionality, data integrity, security, or financial correctness. Blocks release. |
| S2 | Major. Cosmetic, usability, compatibility, or compliance issues that do not block the main flow. |

Severity is set at two levels:
- A baseline for the whole test case.
- A value for each step, so a mostly-S2 test case can still contain an S1 check.

**AC Ref categories**

| Category | Used for |
| --- | --- |
| Scenario N | One test case per written acceptance criterion |
| Cross-Client | Browser, device, OS, or app platform parity |
| Error Handling | Server errors, timeouts, network loss, malformed input |
| Accessibility | Keyboard use, screen readers, focus order, contrast (WCAG 2.1 AA) |
| Reporting | Analytics or audit requirements. Marked N/A when the AC defines none. |
| GAP — not in AC | Behaviour the AC does not define but the feature needs |

**Test design techniques**

| Abbreviation or name | Technique |
| --- | --- |
| EP | Equivalence Partitioning |
| BVA | Boundary Value Analysis |
| Decision Table | Decision Table Testing |
| State Transition | State Transition Testing |
| Error Guessing | Error Guessing |
| Checklist Based | Checklist-Based Testing |

For gap cases, the prompt asks which technique exposed the gap and why. This keeps the reasoning visible when the gap is raised with the team.

## Limitations

- **Review everything.** The prompts give structure, not correctness. Check expected results against the actual requirement, and treat severity ratings and effort estimates as starting points for discussion.
- **Gap cases are questions, not defects.** A gap test case records behaviour the AC does not define. Resolve it with the Product Owner or BA before it becomes a pass/fail check.
- **Watch what you paste.** Do not put confidential ticket content, credentials, customer data, or internal URLs into an AI tool your organisation has not approved.
- **Effort figures assume three QA engineers working in parallel.** Adjust the prompt if your team is a different size.

## Author

Ziyad Al Khalis, QA Engineer

## License

[Add license, e.g. MIT]
