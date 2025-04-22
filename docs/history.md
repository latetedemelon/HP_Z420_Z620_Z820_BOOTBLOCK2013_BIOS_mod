# HP Zx20 Firmware‑Modding History & Key Sources

This page chronicles the community’s multi‑year effort to unlock Ivy‑Bridge‑EP support and modern features on the HP Z420, Z620, and Z820 workstations. Dates are approximate; see linked forum posts and documents for primary evidence.

---

## Timeline of Discoveries

| Year | Milestone | Key Actors / Evidence |
| ---- | --------- | --------------------- |
| **2011 Mar** | HP launches *Z420/Z620/Z820* with **2011 Boot Block** and Sandy‑Bridge‑EP Xeons. | HP marketing materials & BIOS dumps (board silkscreen 61826x‑001). |
| **2013 Jun** | “v2” refresh ships with **Boot Block 2013** and Ivy‑Bridge‑EP CPUs; BIOS rev 3.50 introduces requisite microcode. | HP SoftPaq 3.50 changelog. |
| **2015–2018** | Early tinkerers desolder SPI flash and program Boot Block 2013 with CH341A clips.  Success confirmed but process viewed as **high‑risk**. | Assorted HP forum posts; see PDF guide.  |
| **2019 Nov** | **HashsumCollision** publishes *Zx20 Boot Block Upgrade Guide v1.02* – first comprehensive *hardware* flashing tutorial. | PDF v1.02.  |
| **2021 Jan** | **SuperThunder** hosts GitHub repo detailing address offsets, jumper usage, and adds NVMe DXE + Re‑Size BAR patches to BIOS 3.96. | Original GitHub README. |
| **2024 Apr–May** | **silentbogo** & **bibikalka** discover a **software‑only path**: chaining ME 7→8 flash, BIOS 2.07 downgrade, and FDO + E14 jumpers to enter **Manufacturing Mode**, allowing Boot Block writes via FPT. | TechPowerUp *Workstation Owners Club* |
| **2024 Jul** | Community confirms the method works on Z420, Z620, and Z820 Rev 3 without desoldering; scripts and pre‑cropped images added to repo. | GitHub release notes (J6Y/J8Y 3.96+ variants). |
| **2025 Apr** | This documentation library consolidates different sources into a coherent guide set. |  |

---

## Key SoftPaq Releases

| SoftPaq ID | Contents | Notes |
| ---------- | -------- | ----- |
| **sp59186** | BIOS **2.07** (2012‑07) | Crucial for triggering Manufacturing Mode after ME 8 flash. |
| **sp59990 / sp59991** | **ME 7** → **ME 8** update packages for Z420/Z620 | Used with FDO jumper to unlock ME region. |
| **sp59992 / sp59993** | Same as above for Z820 |  |
| **sp70077 / sp70078** | BIOS **3.85** (pre‑Spectre) | Source of 2013 Boot Block in early guides. |
| **sp82644 / sp82684** | Latest ME 8.1.72 corp firmware (2024) | Optional, mitigated version. |

(Direct FTP links are listed in *upgrade‑guide.md* and forum posts; HP periodically reorganises their server, so mirrors are also kept in the repo.)

---

## Notable Forum Threads & Documents

* **HashsumCollision – Zx20 Boot Block Upgrade Guide v1.02** (PDF) – foundational theory & hardware flashing instructions. 
* **TechPowerUp – HP Workstations Owners Club** – pages 11‑14 cover Manufacturing Mode breakthrough. 
* **HP Support Forum threads** – ME firmware flash instructions, jumper diagrams, user success reports.
* **Win‑RAID – Intel ME System Tools** – provides the `FPT`, `Flash Image Tool`, and clean ME images essential for the mod.
