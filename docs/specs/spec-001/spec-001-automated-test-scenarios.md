# Automated test scenarios for project setup

Run against the application repository revision under review. References R1–R4 and AC1–AC4 denote the requirements and acceptance criteria in their listed order. Use Git, JDK 25, and the Maven installation or launcher documented in the root README. The expected entry point is the application's existing entry point. Take the expected visible behavior from the README before running the test.

### 1. Clone using the first README step (R3, AC4)

- **Start and inputs.** Use an empty parent directory, the root README at the revision under review, and the application repository URL supplied for the review.
- **Actions.** Locate the first numbered setup step. Execute its clone command verbatim in the documented shell. Follow the README instruction for entering the checkout. Inspect the Git worktree root and origin. If reviewing an unmerged change, select the reviewed revision after checking the clone, then use its README for the remaining checks.
- **Pass.** The first step contains an executable Git clone command. It succeeds without substituting a URL or directory placeholder. The documented directory resolves to the root of the cloned application repository, and its origin identifies the expected repository. Both the root README and root POM exist at the reviewed revision.

### 2. Verify the Java release and dependency configuration (R1, AC3)

- **Start and inputs.** Use the reviewed checkout, its root POM, and the README's Maven environment settings.
- **Actions.** Check the JDK used by Maven. Inspect the effective compiler configuration for the documented build, resolving inherited settings, active profiles, and property expressions. Inspect the project's resolved dependencies across all scopes. Maven's own build plugins and their implementation libraries are build tooling.
- **Pass.** Maven runs on JDK 25. Compilation targets Java release 25, and the application project declares or inherits zero external project dependencies.

### 3. Build and launch exactly as documented (R1–R4, AC1, AC4)

- **Start and inputs.** Use a fresh checkout with no prior build output or IDE setup. Use the README's commands, arguments, environment settings, and working directory.
- **Actions.** Run the documented Maven build command from the project root. Inspect the produced application artifact and its compiled entry-point class. Run the documented Maven launch command. Observe the launched application's entry point and the success condition stated in the README. For a visible-only result, verify the JVM entry point automatically and use the manual observation below the scenario list.
- **Pass.** The build succeeds and produces the artifact containing the compiled application entry point for Java 25 (class-file major version 69). The launch invokes that entry point and reaches the documented result. A successful Maven exit alone is insufficient evidence of application startup.

### 4. Repeat setup in a path containing spaces (R1, R2, R4, AC1)

- **Start and inputs.** Clone into a fresh parent directory named `Project Setup Check`. Use the same tool versions and README steps as in scenario 3.
- **Actions.** Build and launch once. After the first run finishes or is closed normally, remove only the generated build output in the disposable checkout. Repeat the same documented build and launch from that checkout.
- **Pass.** Both builds succeed and produce the application artifact. Both launches use the same entry point and produce the documented result, including after the previous build output has been removed.

If startup is observable only in a window, capture the Maven-launched window for comparison in manual scenario 1. Keep that visual verdict open until the tester compares both launches.
