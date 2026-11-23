# Code Review Standard

The reviewer evaluates the pull request (PR) against the specification, its companion files, and the applicable project and implementation standards. The reviewer reports findings and proposed improvements to the user.

## 1. Establish the review scope

The user supplies the PR link, the specification file, and any companion files for the same specification number. Read these files in full, together with the applicable project and implementation standards. Record the PR base branch and the head commit reviewed. Inspect the diff against the base and read surrounding code as needed to understand the affected behavior.

Companion files are optional. If a supplied file is inaccessible, a referenced companion is missing, or two requirements conflict, identify the affected checks and ask the user for the missing input or decision. Keep those checks open.

## 2. Verify requirements and scenarios

Trace every requirement, acceptance criterion, and companion-file entry to evidence at the reviewed commit. Use its identifier, scenario number, or a short quotation so the user can locate it.

| Review input | What the reviewer verifies |
|---|---|
| Specification | Follow each required behavior through its normal entry point to the observable outcome. Check the stated constraints, dependencies, and edge cases. Flag missing requirements, regressions introduced by the change, and added behavior outside the approved scope. |
| Automated test scenarios | Locate an executable test for each scenario. Check its setup, action, and assertions against the specified outcome. Run the relevant tests or inspect matching CI results for the reviewed commit. Record both implementation coverage and execution status. |
| Manual test scenarios | For each numbered row, check that the workflow can be performed and the specified evidence can be collected. Compare the evidence from the test run with the pass condition in Result and the tester's Conclusion. A blank Conclusion means execution is pending. A recorded verdict without the required evidence remains unverified. |
| Logging requirements | Treat every entry as mandatory. Verify the event, emission point, recorded values, output location, and means of identifying the relevant run. Check that the resulting evidence supports the linked manual scenario. |

## 3. Check compliance with existing standards

Apply each relevant rule in `PROJECT STANDARD.md` and the implementation standards to the changed code and affected workflows. For each violation, cite the specific rule and the evidence. Include applicable process requirements such as branch selection and root README accuracy. If a check depends on information outside the PR, identify the evidence needed to verify it.

## 4. Research applicable practices

Perform a targeted web search for every PR. Base the search on the actual change and the versions used by the project. Prefer official specifications, version-matched API documentation, and guidance from the technology's maintainers. For example, when a change alters a Vulkan object's lifetime, consult the documentation for that object and its destruction requirements.

Cite the sources consulted and explain how they apply. If the code violates an API contract or introduces a demonstrable defect, report the concrete impact as a PR finding even when the project standard is silent.

When the research identifies a recurring practice worth adding to `PROJECT STANDARD.md`, check whether an existing rule already covers it. Present each proposed addition or revision to the user with:

- The exact proposed wording, including when the rule applies and how to verify compliance.
- The PR example that prompted it and why the rule would benefit future changes.
- An authoritative source and any relevant version or platform limits.

The project owner decides whether to adopt the proposal. A proposal alone does not block the PR. If the search yields no justified addition, say so briefly. If external research could not be completed, disclose that limitation.

## 5. Report the outcome

Identify the reviewed commit and give one verdict: **Changes required** for a confirmed defect or unmet requirement, **Verification incomplete** when an open check prevents a conclusion, or **Meets requirements** when all required checks are supported by evidence. Report open checks even when a confirmed defect already warrants changes.

1. **Findings.** List confirmed issues in order of impact. Give the file and line, or the requirement reference for a missing implementation. Explain the expected behavior, the observed discrepancy, and its consequence.
2. **Coverage and verification.** Summarize each requirement and scenario with its implementation evidence and verification status. Identify tests run, CI results inspected, manual evidence reviewed, and checks still pending. A passing test suite alone cannot establish that every required scenario has a test.
3. **Project standard proposals.** Present research-backed recommendations separately from required PR fixes so the user can decide on each proposal independently.
