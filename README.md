# Python Security Tools

A collection of Python tools I'm building while learning networking and security. The tools are at different stages, so check each folder for its own instructions and requirements.

## Projects

### 1. Port scanner

Scans selected ports and reports which ones respond. The script also attempts basic service identification and banner collection.

[Open folder](port-Scanner/)

### 2. Web log analyser

Reads web server logs and flags patterns worth checking, such as repeated HTTP 401 or 404 responses and unusually high request counts from one address. These patterns can have legitimate causes, so review the logs before treating them as an attack.

[Open folder](log-Analyser/)

### 3. Hash cracker

A dictionary-based exercise that checks whether a hash matches a value in a supplied wordlist. Use only hashes you own or have permission to test.

[Open folder](hash-Cracker/)

### 4. Wordlist generator

Generates candidate strings from user-provided inputs for controlled password-auditing exercises.

[Open folder](Wordlist-Generator/)

### 5. ESP32 project

The repository also contains an ESP32 Bluetooth project. Review its code and local documentation before using any hardware. Do not use equipment to interfere with other people's devices or communications.

[Open folder](ESP-32/)

### 6. Hackathon notes

CTF reference notes from hackathon practice.

[Open folder](Hackathon/)

## How to use the projects

Each tool has its own files and may have different requirements. Read the README in that folder and check the source before running it. For the Wordlist Generator, the current script is named `wordgen-1.py`, so use the path that matches the file in the folder.

## Safety

Use the tools on your own systems, local test data or environments where you have permission. Do not scan public systems or attempt password recovery against accounts or hashes you are not authorised to test. Radio jamming and interference can be illegal and can affect other devices; do not operate jamming functions.

## Limitations

These are learning projects, not production security products. Detection results depend on the input data and implementation. Review the code and verify findings before drawing conclusions.

## Author

Ujwal Vishwanath  
BCA Cybersecurity and Digital Forensics, REVA University
