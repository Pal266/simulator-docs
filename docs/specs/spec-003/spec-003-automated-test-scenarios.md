# Automated test scenarios for the settings framework

R1–R5 and AC1–AC7 refer to the requirements and acceptance bullets in their listed order. All scenarios can be automated. This specification requires no manual checks.

Run filesystem scenarios on Windows under a disposable user profile and checkout using Java 25. Run cases independently unless a scenario specifies a sequence. Compare serialized entries without assuming key order or a particular line ending. Exercise the application's actual read and write methods.

Scenarios 1 and 2 use the shipped application model. Scenarios 3–6 use the same framework with two test-only fields: integer `count` defaults to 10 and accepts 1 through 20 inclusive; string `label` defaults to `base` and accepts any nonempty single-line text. These names and rules are fixture data.

### 1. Startup order, file selection, and mode isolation (R1–R3, AC1)

- **Setup.** Place a UTF-8 file named `settings.cfg` containing an arbitrary key in each expected directory. In development the expected directory is `logs` beside the application's root POM. In production it is `Game` inside the test user's actual Windows Documents folder. Place a third file with different contents under `logs` in a temporary directory outside the project.
- **Action.** Launch in each mode, using the outside directory as the development process working directory. Observe startup and file access with test-side instrumentation. Capture successful completion of logging initialization, the path actually read, completion of the read, and the settings object seen by the first consumer. If there is no consumer yet, observe the object at startup completion.
- **Pass.** The read begins after logging initializes, uses the expected mode-specific file, and completes before settings are consumed. The resolved object is available and contains zero application settings. The decoy and inactive mode files are untouched. This path observation is necessary because either file would produce an empty application object.

### 2. Empty application model and explicit overwrite (R1–R3, R5, AC2)

- **Setup.** In each mode, start with the active settings file absent.
- **Action and pass conditions.** Launch the application. Startup succeeds with an empty settings object and the file remains absent. Call `writeFile()` and confirm the active file exists and is exactly zero bytes. Populate it with `count=7` and `label=external` on separate lines. Call `readFile()`: the application still exposes zero settings, including zero recognized fixture keys. Call `writeFile()` twice and check the size after each call. It is zero both times. Relaunch and verify that the object remains empty and the file is still zero bytes.

### 3. Read from scratch and serve settings from memory (R2, R4, AC3)

- **Setup.** Start a fresh fixture with no file and read. Expect `count=10`, `label=base`, and no file creation.
- **Action and pass conditions.** Create a file with `count=19` and `label=custom`; read and verify both typed values through the consumer. Replace the file with only `label=edited` and inspect the consumer before rereading: it still sees 19 and `custom`. Read again and expect 10 and `edited`. Delete the file and read once more: both fields return to their defaults, the object remains available, and the file stays absent.

### 4. Field conversion, validation, and first occurrence (R2–R4, AC4)

For each row, start with a file containing `label=independent` and the listed additional lines in order. Call `readFile()`. The label must be `independent` in every case. Assert the integer shown below.

| Additional count entries, in order | Expected count |
|---|---:|
| No entry | 10 |
| `count=abc` or `count=` (separate runs) | 10 |
| `count=0` or `count=21` (separate runs) | 10 |
| `count=1` | 1 |
| `count=20` | 20 |
| `count=7`, then `count=14` | 7 |
| `count=abc`, then `count=14` | 10 |
| `count=21`, then `count=14` | 10 |

### 5. UTF-8 syntax and ignored lines (R3–R4, AC4–AC5)

Write the following UTF-8 text verbatim, including the blank line and spaces. Call `readFile()` on a fresh fixture.

```text
   # count=2

count
unknown=3
COUNT=4
 count = 8
count=9
 label =  Příliš=žlutý
```

**Pass.** The fixture contains integer 8 and string `Příliš=žlutý`, with no other settings. The malformed and differently cased lines do not claim the lowercase key. The first recognized count wins after trimming. The label retains its second equals sign and Unicode characters.

### 6. Create, overwrite, repeat, and reload (R1, R3–R5, AC6–AC7)

- **Setup.** In each mode, use a fresh fixture with no active file. Set the in-memory label to `město=Praha` and leave count at its default.
- **Action and pass conditions.** Call `writeFile()`. Decode the new file as UTF-8 and inspect entries before any mapping can collapse duplicates. Expect exactly one `count` entry representing 10 and one `label` entry representing `město=Praha`. A fresh fixture reading it obtains those typed values.
- **Overwrite check.** Replace the on-disk file with two conflicting count entries, an unknown key, and a label of 4,096 ASCII characters. Without rereading, change the original in-memory fixture to 3 and `短=x`. Write it twice. After each write, inspect the whole file: each supported key occurs once, only the two current values remain, and there is no residual text from the longer file. Reading into a fresh fixture returns integer 3 and the exact Unicode string.
