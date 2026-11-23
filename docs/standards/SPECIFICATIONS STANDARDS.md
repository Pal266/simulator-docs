1. Filename. Use spec-xxx-short-meaningful-name.md, where xxx is a unique three-digit specification number. Write the short name in lowercase and separate words with hyphens. Example: spec-001-project-setup.md.

2. Structure. Every specification must contain these four elements in this exact order.
| Element | Rule |
|---|---|
| **Title** | State the main goal in one short phrase. |
| **Type** | Use the type supplied by the user. If it is missing, ask for it before finalizing the specification. |
| **Requirements** | List the changes the implementer must make. Each requirement must be unambiguous and verifiable. |
| **Acceptance criteria** | List the verifiable conditions that demonstrate completion. Cover every requirement and keep each criterion within the scope of the requirements. |
3. Implementation completeness. Include all information needed to implement and verify the change independently. Describe any existing behavior the change relies on. Attach or directly link each required asset, including UI resources, under the requirement that uses it. Links to other specifications may provide background. All requirements must be stated in the current specification.
4. Dependencies and libraries. Under the relevant requirement, name each dependency or library the implementation agent will use and state its purpose. Give the required version, a supported version range, or a reference to the project file that pins the version.
5. Implementation details. State the required outcomes, observable behavior, and constraints. Leave internal design decisions to the implementation agent.