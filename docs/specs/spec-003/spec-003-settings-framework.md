# Add settings framework

## Type

`feature`.

## Requirements

1. Deliver the settings framework with zero application settings fields and zero recognized application keys. Expose two Java methods, `readFile()` and `writeFile()`, operating on the active mode’s `settings.cfg` and the application's in-memory settings object. Use the existing logging directory resolution. In development, the file is directly under `logs` beside the application’s root `pom.xml`, regardless of the working directory. In production, it is directly under `Game` in the current Windows user’s Documents folder.
2. After logging initializes successfully, call `readFile()` before any component consumes settings. On every read, start with the declared defaults for every settings field. If the file is missing, make the fully defaulted object available in memory and leave file creation to `writeFile()`.
3. Use Java 25 standard library file I/O to read UTF-8 text, one case-sensitive `key=value` entry per line. Ignore blank lines, full-line comments beginning with `#` after optional whitespace, lines without an equals sign, and unknown keys. Split at the first equals sign and trim surrounding whitespace from the key and value.
4. Map each supported key to a typed Java field with a declared default and validation rule. For each key, process only its first occurrence in file order. Assign the parsed value if conversion and validation succeed. Otherwise keep that field’s default and continue processing other keys. Application components use the resolved object in memory.
5. On an explicit `writeFile()` call, serialize the current values of all supported fields to the active settings file as UTF-8 entries that `readFile()` can read back. Include fields whose current value equals their default. Create the file if missing or replace its complete contents if present. Write each key exactly once. The result reflects the object at the time of the call, independent of the previous file contents.

## Acceptance criteria

- In each mode, startup reads the settings file from the specified directory after logging initialization. Changing the process working directory leaves the development file location unchanged.
- With the empty application settings object, startup succeeds when the file is missing and leaves it absent. Reading a file containing arbitrary keys also yields an empty object. In each mode, an explicit write creates an empty file if missing or truncates an existing file to zero bytes. A second write and a subsequent launch preserve that result.

Verify the following field-level behavior using test fixtures that exercise the same framework code. Fixture fields exist only in tests.

- Read a file containing valid nondefault values, then remove it and call `readFile()` again. All fields revert to their declared defaults, the object is available to consumers, and the file remains absent. Starting the application with no file produces the same result.
- Verify absent, unconvertible, and out-of-range values independently alongside a valid value for another field. Only the affected fields take their defaults. With two entries for the same key, a valid first value wins; an invalid first value followed by a valid value yields the default.
- Blank lines, comments, unknown keys, and lines without an equals sign leave recognized settings unaffected. A value containing an equals sign retains the text after the first separator when mapped to a compatible field.
- In each mode, call `writeFile()` with no file present. The new file contains exactly one entry per supported field, including default-valued fields. Reading it restores the same typed values.
- Seed an existing file with duplicate entries, an unknown key, and stale values. Change a field in memory and call `writeFile()` twice. After each call the file contains only the current settings, once per field, with no stale entries or trailing data. Reading the file into a fresh fixture object restores the values last written.
