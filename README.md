# Python Security Tools

A collection of small Python tools I have been building while learning networking and security. Each tool has a different purpose, so check its source and requirements before running it.

## Tools

### Port scanner

Scans selected ports and reports which ones respond. The current script also attempts basic service identification and banner collection.

### Hash cracker

A dictionary-based exercise for checking whether a hash matches a value in a supplied wordlist. Use it only with hashes you own or have permission to test.

### Web log analyser

Reads web server logs and highlights patterns that may deserve a closer look, such as repeated HTTP 401 or 404 responses and unusually high request counts from one address. These patterns are indicators, not proof of an attack, and can produce false positives.

### Wordlist generator

Generates candidate strings from user-provided inputs for controlled password-auditing exercises. Do not use personal information about someone else to build targeted wordlists without explicit authorisation.

## Running the tools

The scripts are separate utilities and may have different requirements. Check each script and its dependencies before running it. The Wordlist Generator can be started with:

```bash
python3 wordgen.py
```

The exact options and output depend on the script version in this repository.

## Testing

Test tools against your own machine, local test data or an environment where you have permission. Do not scan public systems or attempt password recovery against accounts or hashes you are not authorised to test.

## Limitations

These are learning projects, not production security products. Detection results depend on the input data and current implementation. Review the code and verify findings before drawing conclusions.

## Author

Ujwal Vishwanath  
BCA Cybersecurity and Digital Forensics, REVA University
