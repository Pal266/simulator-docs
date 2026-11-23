# Automated test scenarios for the logging framework

Run on Windows with a disposable user profile and checkout. R1–R6 refer to the numbered requirements. AC1–AC10 refer to the acceptance bullets in order. Use the application's launch path and its documented development selector. Determine the expected Documents directory independently from the test account's Windows Documents location. Discover the filename on the first successful launch in each mode and retain it for later comparisons.

For startup-order checks, observe the first call that begins another application system. If none exists yet, use a test probe at the point immediately after logging initialization in the application startup sequence. Use a test-side signal to record whether that point was reached. Keep probe output separate from the application log.

### 1. Select the mode and create the directory (R2, AC1, AC2, AC8)

- **Setup and inputs.** Use clean target directories that do not yet exist. Run once with all mode selections unset and once using the Maven development instructions copied from the root README. Repeat the development launch with the application process working directory set to an existing directory outside the checkout. Keep the same application checkout and root POM.
- **Action.** Launch each case independently. Record the application's working directory and the full path of the file containing this run's startup entry.
- **Pass.** The default launch creates and writes directly inside the test user's Documents/Game directory. The documented Maven invocation creates and writes directly inside the logs directory beside the application's root POM. The outside-directory launch writes to the same development file, without creating a log under the working directory. The unselected mode's log is untouched.

### 2. Verify startup order and UTC format (R1, R4, AC3, AC4)

- **Setup and inputs.** Exercise both modes on a test machine using a non-UTC local time zone. Record UTC time to millisecond precision immediately before launching.
- **Action.** At the startup boundary described above, read the on-disk file before the next system performs any work. Record the observation time in UTC.
- **Pass.** A complete startup line is already visible and occurs exactly once. Its message is exactly `Logging framework successfully instantiated.`, including the period. Its prefix parses as a valid UTC instant in `yyyy-MM-dd'T'HH:mm:ss.SSS'Z'` form, with exactly three fractional digits and a literal Z. The instant is within the inclusive launch-to-observation interval. Exactly one space, a hyphen, and one space separate timestamp and message.

### 3. Append application messages (R4, AC9)

- **Setup and inputs.** In each mode, initialize the application and capture its startup line. Through an application component, submit `first - item` and then `second item` in two sequential calls. Record UTC bounds around each call to millisecond precision.
- **Action.** Read the completed log and locate the startup line and each supplied message.
- **Pass.** The original startup line is retained. Each message appears exactly once, on a separate line, in submission order after startup. Each has the timestamp format and separator from scenario 2, with the event time inside the corresponding call's bounds. Compare the entire message, including the separator-like text inside the first message. Equal millisecond timestamps are valid.

### 4. Replace prior contents at the stable path (R3, R4, AC4, AC5)

- **Setup and inputs.** In each mode, let a successful instance exit. Add a unique marker and a second copy of the old startup line to its log.
- **Action.** Relaunch in the same mode and inspect the file at the startup boundary.
- **Pass.** The same path contains exactly one fresh startup entry satisfying scenario 2. The marker and the extra old line are gone.

### 5. Reject overlapping processes and permit a later launch (R1, R3, AC10)

- **Setup and inputs.** In each mode, keep process A running after it submits `A before B`. Wait until that entry is visible, pause further writes from A, and snapshot the file bytes.
- **Action.** Start process B against the same log. Capture B's standard error, exit status, and startup-boundary signal. Compare the file with the snapshot before asking A to submit `A after B`. After A exits, start C. Repeat the recovery step in a separate run after forcibly terminating the disposable A process and waiting for its exit.
- **Pass.** B exits nonzero before the next system starts. Its standard error identifies an active-instance conflict and the target path. The snapshot matches byte for byte after B exits. A can append the second message. Once A has exited, C starts at the same path with one fresh startup entry and no entries from A.
- **Simultaneous-start variant.** Release two independent launch processes together against an unused target. Hold whichever instance initializes successfully open until the other exits. Exactly one reaches the startup boundary. The other meets B's rejection conditions, and the successful instance's startup line remains intact.

### 6. Abort on startup I/O failures and retry (R1, R6, AC7)

For each row, exercise both modes with no other instance running. Preserve the path before launch. Capture process standard error, exit status, and the startup-boundary signal. Use deterministic test-side fault injection for operations that the environment cannot fail independently. Simulate only the indicated I/O failure and exercise the application's actual startup error path.

| Failed operation | Setup or injected fault | Expected diagnostic identifies |
|---|---|---|
| Create target directory | Place a regular file at the required directory path. | Directory creation and the target directory path |
| Open the log | Create a directory at the known log filename inside the valid target folder. | Opening the log and the target file path |
| Truncate the log | Seed a prior log and cause the operation that opens it for replacement or truncates it to fail. | The failed open-for-replacement or truncate operation and the target file path |
| Write startup entry | Allow the file to open and truncate. Fail the write of the startup entry, including a buffered write that reports failure when flushed during initialization. | Writing the startup entry and the target file path |

**Pass for every row.** The diagnostic appears on standard error, the exit status is nonzero, and the next system never starts. Remove the obstruction or injected fault and relaunch against the same destination. Initialization now succeeds and the fresh startup entry is visible. The specification does not require recovery of prior file contents after a failed truncate or write.

### 7. Build and run logging with the JDK and project code (R5, AC6)

- **Setup and inputs.** Isolate the logging code and the project code it uses. Supply the project JDK and a small project-code caller. Exclude third-party libraries from the compile and runtime classpaths for this check.
- **Action.** Compile the logging path and caller, initialize logging, and submit a single-line message through the framework.
- **Pass.** Compilation and execution succeed, and the file contains the startup entry and supplied message. Neither operation requires an external logging or helper library.
