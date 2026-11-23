# Add logging framework

## Type

`feature`.

## Requirements

1. On Windows, initialize the logging framework as the first application system on every launch. Complete initialization and write the startup entry before initializing any other application system.
2. Select production mode by default. Provide an explicit way to select development mode and document how to use it from Maven and IntelliJ IDEA in the root `README.md`. The project root is the application directory containing the root `pom.xml`, regardless of the process working directory. In development mode, write the log file directly in the `logs` folder under the project root. In production mode, write it directly in the `Game` folder under the current Windows user's Documents folder. Create the target folder if it is missing.
3. Use the same log file path on every launch within each mode. While one instance is running, reject a second launch targeting the same log file before it can modify the file or initialize another application system. Report the active instance and target path on standard error and exit with a nonzero status. On the next successful launch, replace the file contents so that only entries from the current launch remain.
4. After initialization, application components must be able to submit messages through the logging framework. Append each submitted message to the current log in submission order for sequential calls. Format each entry as one line containing the timestamp of the event, one space, a hyphen, one space, and the message. Use UTC and the exact timestamp format `yyyy-MM-dd'T'HH:mm:ss.SSS'Z'`, with three fractional digits and a literal trailing `Z`. On successful initialization, write exactly one entry with the message `Logging framework successfully instantiated.`
5. Use only the Java standard library and project code for logging. The logging framework must require no external dependencies.
6. If startup cannot create the target directory, open or truncate the log file, or write the startup entry, abort before initializing any other application system. Write a diagnostic to standard error identifying the failed operation and its target path, then exit with a nonzero status.

## Acceptance criteria

- On a Windows development launch with no project-root `logs` folder, startup creates the folder and writes the log file directly inside it.
- On a Windows production launch with no `Game` folder in the current user's Documents folder, startup creates that folder and writes the log file directly inside it.
- When initialization of the next application system begins, the logging framework is ready and the startup entry is already present in the file.
- In either mode, the file contains exactly one startup line after initialization. Its timestamp parses as UTC in the format specified in requirement 4 and falls within the startup interval measured to millisecond precision, and everything after the timestamp is exactly ` - Logging framework successfully instantiated.`
- In each mode, adding a marker to the file and relaunching leaves the file at the same path with exactly one fresh startup entry. The marker and the prior entry are gone.
- The logging code builds and runs using Java standard library and project code alone.
- In either mode, each failure in requirement 6 produces a diagnostic on standard error identifying the failed operation and the affected directory or file path. The process exits with a nonzero status before any other application system starts. A regular file occupying the required directory path is one reproducible failure case.

- With no mode selection, a Windows launch writes to the production location. Following the root README's development instructions from Maven and from IntelliJ IDEA writes to the development location. Launching in development mode with a working directory outside the project still writes to the same project-root log.
- In either mode, submit two distinct single-line messages sequentially through the initialized framework. The file retains the startup entry and contains each submitted message exactly once after it, in submission order, each on its own line with the timestamp and separator specified in requirement 4.
- In each mode, keep instance A running after it writes a test message. Record its log contents, then launch B against the same file while A is idle. B exits with a nonzero status before initializing another application system. Its standard error identifies the active-instance conflict and the target path. The file contents are unchanged, and A can append another message. After A exits, a new launch succeeds at the same path with one fresh startup entry.

Example startup line: `2026-11-23T13:42:18.037Z - Logging framework successfully instantiated.`
