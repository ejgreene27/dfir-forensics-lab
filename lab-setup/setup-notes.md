# Lab Setup Notes

## SIFT Workstation VM Configuration

### Initial Setup
- **Source:** SANS SIFT Workstation OVA
- **Hypervisor:** UTM on macOS (Apple Silicon M3)
- **Emulation:** x86_64 (required — SIFT is not ARM-native)
- **RAM:** 8 GB
- **CPU Cores:** 4
- **Disk:** 98 GB virtual disk (~74 GB free after OS)
- **Network:** Shared Network (e1000 adapter)
- **Storage Location:** Samsung T7 1TB SSD → `CyberLab/DFIR/VMs/`

### Boot Issue — UEFI Shell Fix
On first boot, the VM dropped into a UEFI Interactive Shell instead of booting the OS. The UEFI firmware could see raw disk partitions (BLK0–BLK3) but no mountable filesystems (no FS0 mapping), so GRUB could not be found.

**Resolution:** Powered off the VM → UTM Settings → QEMU → Tweaks → Unchecked "UEFI Boot" to switch to legacy BIOS mode → VM booted successfully into Ubuntu/SIFT.

### Tool Verification
- **Autopsy:** Pre-installed at `/usr/bin/autopsy`
- **Volatility 3:** Not pre-installed on this SIFT build. Installed manually:
  ```
  pip3 install volatility3 --break-system-packages
  ```
  Added to PATH:
  ```
  echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc && source ~/.bashrc
  ```
  Verified with `vol --help` — Volatility 3 v2.27.0

### Shared Folder
UTM shared directories did not mount automatically under x86_64 emulation. Forensic images will be downloaded directly into the VM via `curl`/`wget` instead, which mirrors real-world evidence acquisition workflows.

### Credentials
- **Username:** `sansforensics`
- **Password:** `forensics`

### Display Note
UTM intermittently shows "Display output is not active" — this is the display sleep timeout. Clicking the VM window restores the display. Can be resolved by disabling screen blanking inside the VM if needed.
