# Chemistry - Easy(Linux)
## Summary
Chemistry is a Linux machine where initial access is gained by exploiting insecure file upload functionality in a Python-based CIF analysis web application. After obtaining credentials from a local SQLite database, SSH access is achieved. Privilege escalation to root is performed by leveraging a Path Traversal vulnerability in the aiohttp library (CVE-2024-23334), allowing access to sensitive system file
