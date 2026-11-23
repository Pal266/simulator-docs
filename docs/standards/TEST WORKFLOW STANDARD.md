# Test Workflow Standard

## 1. Determine coverage

Read the entire `spec-xxx-short-meaningful-name.md` file. Use its three-digit specification number in every output filename. Produce up to three Markdown files per specification according to the rules below. Omit files with no applicable content.

Cover every acceptance criterion and every testable requirement. Identify the relevant requirement or criterion in each scenario by its existing identifier or a short quotation. Include successful outcomes and relevant edge cases, such as boundary values, invalid inputs, and failure conditions. Group inputs that exercise the same condition and have the same expected outcome.

If the expected behavior is ambiguous or missing, ask the specification author for clarification and continue with the independent scenarios. Keep the affected coverage open until the expected outcome is defined.

## 2. Automated test scenarios

Create `spec-xxx-automated-test-scenarios.md` when the project's supported tools and test environment can perform an action and verify its outcome. Classify a scenario as automated even if its test code has yet to be written.

For each scenario, identify the behavior and its source requirement or criterion. Specify the starting conditions, test inputs, actions, and observable pass condition. When all checks are automated, state that the specification requires no manual checks.

## 3. Manual test scenarios

Create `spec-xxx-manual-test-scenarios.md` for checks that require human action or observation. Identify the specific step or judgment that requires a tester. If a requirement has both automated and manual checks, document each check once.

Use one row per scenario with the following five columns.

| Number | Test scenario | Workflow | Result | Conclusion |
|---|---|---|---|---|
| Sequential number starting at 1 | Behavior under test, source requirement or criterion, and reason for manual verification | Prerequisites, exact test data, and numbered steps another tester can reproduce | Observable pass condition and the evidence the tester must capture to determine the verdict | Leave blank until the scenario is executed. Record `Success` if the observed result meets the pass condition, or `Failure` if it does not. |

Define the expected state or values precisely enough for two testers following the same steps to reach the same verdict. For screenshot evidence, identify the visible state to capture. For log evidence, identify the event and expected field values, or how to derive those values from the test inputs. The Result column specifies the expected evidence before execution.

## 4. Logging requirements

Create `spec-xxx-logging.md` when a manual check requires log evidence that existing outputs cannot supply. **Every entry in this file is a mandatory logging requirement.** The implementation agent must implement each entry according to the implementation standards. The logging file itself establishes the requirement.

For each required log entry, specify:

- The manual scenario number or numbers that use it.
- The event that must produce it and the point in the workflow when it must be emitted.
- The information it must record so the tester can distinguish the expected outcome from a failure.
- Where the tester can retrieve it and how to match it to the operation performed during that run.

Reference the required entry in the manual scenario's Result column. When an existing log already provides sufficient evidence, identify it directly in that column.

## 5. Verify coverage

Before finalizing, check that each testable requirement, acceptance criterion, and distinct specified outcome maps to at least one scenario. Confirm that each manual scenario has a reproducible workflow and a decisive pass condition, and that each required log supplies evidence used by an identified manual scenario.

## 6. Try to break the application. 
For each feature under test, ask what a user could do to make it crash, lose data, or produce an incorrect result. Consider repeating an action, performing steps out of order, or interrupting an operation and retrying it. For each plausible failure within the specification’s scope, design a reproducible scenario and state what the tester must observe to confirm the application handled it correctly.
