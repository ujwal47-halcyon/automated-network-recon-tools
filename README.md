# Python Security Tools

A collection of Python tools I'm building while learning networking and security. The projects are at different stages, so check each script and its requirements before running it.

## Tools

### 1. Port scanner

Scans selected ports and reports which ones respond. The script also attempts basic service identification and banner collection.

### 2. Web log analyser

Reads web server logs and flags patterns worth checking, such as repeated HTTP 401 or 404 responses and unusually high request counts from one address. These patterns can have legitimate causes, so review the logs before treating them as an attack.

### 3. Hash cracker

A dictionary-based exercise that checks whether a hash matches a value in a supplied wordlist. Use only hashes you own or have permission to test.

### 4. Wordlist generator

Generates candidate strings from user-provided inputs for controlled password-auditing exercises. Do not use someone else's personal information to build targeted wordlists without explicit authorisation.

## Running the tools

The scripts are separate utilities and may have different requirements. Check the source code and any project-specific instructions before running them.

The Wordlist Generator can be started with:

```bash
python3 wordgen.py
```

Check the script's current options for the available commands and output format.

## Suggested project workflow

For each tool, document the requirements, example command, sample output, tests and known limitations. Add examples only after confirming that they match the current code.

## Safety

Use the tools on your own systems, local test data or environments where you have permission. Do not scan public systems or attempt password recovery against accounts or hashes you are not authorised to test.

## Limitations

These are learning projects, not production security products. Detection results depend on the input data and implementation. Review the code and verify findings before drawing conclusions.

## Author

Ujwal Vishwanath  
BCA Cybersecurity and Digital Forensics, REVA University
