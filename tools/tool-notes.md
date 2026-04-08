# Tool Notes & Reference

Quick reference for tools used across cases. Updated as new techniques are learned.

## Volatility 3

Memory forensics framework for analyzing RAM dumps.

### Common Commands
```bash
# Identify the OS profile of a memory image
vol -f <memory_dump> windows.info

# List running processes
vol -f <memory_dump> windows.pslist

# List processes as a tree
vol -f <memory_dump> windows.pstree

# Scan for hidden/terminated processes
vol -f <memory_dump> windows.psscan

# List network connections
vol -f <memory_dump> windows.netscan

# List loaded DLLs for a process
vol -f <memory_dump> windows.dlllist --pid <PID>

# Dump a process executable
vol -f <memory_dump> windows.dumpfiles --pid <PID>

# Extract command line arguments
vol -f <memory_dump> windows.cmdline
```

## Autopsy

GUI-based digital forensics platform for disk image analysis.

### Key Features
- File system browsing and recovery
- Keyword search across disk images
- Timeline generation
- Hash lookup and filtering
- Web artifact analysis (browser history, downloads)
- Registry analysis

### Workflow
1. Create a new case (Case Name, Examiner Name)
2. Add a data source (disk image file)
3. Run ingest modules (hash lookup, keyword search, web artifacts, etc.)
4. Review results in the tree view
5. Tag and bookmark relevant artifacts
6. Generate report

---

*Notes will be expanded as tools are used in cases.*
