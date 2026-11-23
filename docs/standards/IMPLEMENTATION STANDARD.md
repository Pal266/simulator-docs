# Implementation Standard

1. **Read the specification.** Read `spec-xxx-short-meaningful-name.md` in full to identify the requirements, acceptance criteria, and required dependencies.

2. **Follow the project standard.** Before implementation, read `PROJECT STANDARD.md` in full. Apply every rule relevant to the change and verify compliance before reporting completion. If a specification or companion-file requirement conflicts with a project rule, stop implementation, identify the conflicting requirements, and ask the user how to proceed.

3. **Read the companion files.** Check for the following files with the same specification number. Read each one that exists.

   | File | Required action |
   |---|---|
   | `spec-xxx-automated-test-scenarios.md` | Implement automated tests for the listed scenarios and run them. |
   | `spec-xxx-manual-test-scenarios.md` | Read the workflows and expected results to understand how the tester will verify the implementation. Leave **Conclusion** blank until the scenario is executed. |
   | `spec-xxx-logging.md` | Implement every listed logging requirement, including the specified event, recorded values, and output location. |

4. **Implement and verify.** Implement the specification using the required dependencies. Check the result against each requirement and acceptance criterion. Report automated test results and identify any manual scenarios awaiting execution.

5. **Create and use the implementation branch.** Before implementation, create a branch from the base branch specified by the user and switch to it. Name it `<spec-type>/<short-meaningful-name>`. Take `<spec-type>` from the specification’s Type field and `<short-meaningful-name>` from its filename by removing the `spec-xxx-` prefix and `.md` extension. For example, `spec-012-save-settings.md` with type `feature` produces `feature/save-settings`. If the base branch is unspecified or ambiguous, stop and ask the user to identify it. Before every commit, verify the current branch. The agent may commit to the implementation branch without further permission. For a commit on any other branch, request explicit permission from the user, identify the target branch and proposed changes, and wait for approval. Each approval authorizes exactly one commit.

6. **Maintain the project README.** For every specification implemented, review `README.md` in the project root and update it wherever the documented steps are missing, outdated, or incomplete. Create the file if it is missing. The instructions must enable a user starting from a fresh clone to set up the local development environment, configure the IDE, compile the application, and run it. Include the clone command as the first step. Document additional topics only when the specification explicitly requires them.

7. **Open a pull request.** After completing the implementation and verification steps, open a pull request from the implementation branch into the user-specified base branch. Use the exact specification title as the PR title. Leave a blank line after the title when presenting the title and description together. In the PR description, list each delivered feature, bug fix, or other completed change as a separate Markdown bullet on its own line. Share the pull request link with the user.
