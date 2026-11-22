# Runtime and development tools

The application uses Python's standard library: sqlite3, http.server, threading, dataclasses, abc, datetime, hashlib, hmac, secrets, json, csv, pathlib, argparse, logging and webbrowser. The GUI uses HTML, CSS and browser JavaScript. There is no external runtime framework, paid service or API key.

The delivered application and test suite were run with Python 3.14.3. Python 3.11 or newer is the supported target. SQLite is supplied by Python; its schema is kept in `itsm/schema.sql`.

Development and evidence tools:

| Tool | Purpose |
|---|---|
| PyInstaller 6.22.3 | Package the Windows demo executable with its Python runtime |
| Git 2.49.0 | Local source history and portable repository bundle |
| Node.js + Playwright 1.62.1 | Browser workflow checks, screenshots and capture |
| Microsoft Edge | Chromium browser used for verification |
| Playwright FFmpeg build 1011 | Browser video capture and timestamp scaling for readable silent playback |
| ReportLab 4.4.9 | PDF report and editable SVG diagrams |
| Poppler | Render PDF pages for layout review |
| Pillow 13.0.0 | Assemble rendered-page contact sheets during review |
| pypdf 6.21.4 | Extract source requirements and verify report pagination/text |
| Python 3.12.14 | Separate document-generation environment |

Rebuild the report with `python scripts/build_report.py` after installing ReportLab 4.4.9. Keep the evidence screenshots and browser results in `output/evidence`. The generator also writes the report text source and diagrams. Browser verification requires Playwright and its FFmpeg support plus a Chromium browser. None of these development dependencies are required by `run.py`.

The workflow recording presents the browser test at one-eighth of the capture speed to make the interface transitions readable. It provides a visual record of the tested application workflows.
