Digital Forensics & Incident Response Lab

A portfolio of digital forensics case reports and analysis documentation, built on a SIFT Workstation home lab environment. Each case follows a structured methodology using industry-standard tools to analyze public forensic datasets.

Lab Environment

Component                Details
--------------------------------------------------------------------
Host                     2024 MacBook Air M3 (16 GB RAM)
Hypervisor               UTM (x86_64 emulation on Apple Silicon)
Forensics VM             SANS SIFT Workstation (Ubuntu-based)
VM Resources             8 GB RAM, 4 CPU cores
Storage                  Samsung T7 1TB external SSD (exFAT)
Primary Tools            Autopsy 4.x, Volatility 3, RegRipper, Plaso/log2timeline

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

Each case follows a consistent structure modeled after real-world DFIR deliverables
  1. Executive Summary - High-level findings in plain language
  2. Scope & Objectives — What was analyzed and why
  3. Evidence Inventory — Source images, hashes, chain of custody
  4. Tools Used — Software and versions
  5. Analysis & Findings — Detailed walkthrough with supporting artifacts
  6. Timeline of Events — Chronological reconstruction
  7. Conclusions & Recommendations — Summary of findings and next steps

Cases

#  Case Name     Image Source     Tools Used     Status
---------------------------------------------------------
- Coming Soon

About

This lab was built as part of a cybersecurity portfolio targeting SOC Analyst and DFIR roles. All forensic images used are publicly available datasets intended for educational use. No real personal data is involved.

Contact

Evan Greene 
Github: @ejgreene27

