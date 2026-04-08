Digital Forensics & Incident Response Lab
A portfolio of digital forensics case reports and analysis documentation, built on a SIFT Workstation home lab environment. Each case follows a structured methodology using industry-standard tools to analyze public forensic datasets.
Lab Environment
ComponentDetailsHost2024 MacBook Air M3 (16 GB RAM)HypervisorUTM (x86_64 emulation on Apple Silicon)Forensics VMSANS SIFT Workstation (Ubuntu-based)VM Resources8 GB RAM, 4 CPU coresStorageSamsung T7 1TB external SSD (exFAT)Primary ToolsAutopsy 4.x, Volatility 3, RegRipper, Plaso/log2timeline
Repository Structure
dfir-forensics-lab/
├── cases/
│   ├── 001-case-name/
│   │   ├── report.md          # Full case report
│   │   ├── evidence-log.md    # Chain of custody / evidence inventory
│   │   └── artifacts/         # Screenshots, timelines, exported artifacts
│   ├── 002-case-name/
│   └── ...
├── tools/
│   └── tool-notes.md          # Tool usage reference and cheat sheets
├── lab-setup/
│   └── setup-notes.md         # Lab build documentation and troubleshooting
└── README.md
Case Report Template
Each case follows a consistent structure modeled after real-world DFIR deliverables:

Executive Summary — High-level findings in plain language
Scope & Objectives — What was analyzed and why
Evidence Inventory — Source images, hashes, chain of custody
Tools Used — Software and versions
Analysis & Findings — Detailed walkthrough with supporting artifacts
Timeline of Events — Chronological reconstruction
Conclusions & Recommendations — Summary of findings and next steps

Cases
#Case NameImage SourceTools UsedStatus—Coming soon———
About
This lab was built as part of a cybersecurity portfolio targeting SOC Analyst and DFIR roles. All forensic images used are publicly available datasets intended for educational use. No real personal data is involved.
Contact
Evan Greene
GitHub: @ejgreene27
