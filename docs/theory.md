# HP Zx20 Firmware Anatomy & Upgrade Theory

> This page explains **why** the upgrade procedure works and what each step does. It is *not* required reading to complete the flash, but it will help you troubleshoot and adapt the method to unusual board revisions.

---

## 1  Firmware Regions & Address Map

The 16 MiB SPI flash (Winbond W25Q128 variant) is subdivided by Intel’s Flash Descriptor:

| Region | Hex Range (bytes) | Purpose |
| ------ | ---------------- | ------- |
| **Descriptor** | 0x000000 – 0x000FFF | Defines region sizes, read/write protections |
| **GbE** | 0x001000 – 0x002FFF | Intel LAN PXE, MAC addresses (×2) |
| **PDR** | 0x003000 – 0x004FFF | Platform data (rarely used on workstations) |
| **ME FW** | 0x005000 – 0x50FFFF | Intel Management Engine (v7 or v8) |
| **BIOS** | 0x510000 – 0xFFFFFF | DXEs, PEIs, microcode, Setup menus, *Boot Block* |
| **Boot Block** | 0xFF0000 – 0xFFFFFF | Crisis‑recovery code & very early init |

*Source:* HP firmware dumps cross‑checked with Intel FIT C v8.1 and the Win‑RAID community. citeturn0file0

### 1.1 Why 2013 Boot Block Matters
HP split Zx20 production into two epochs:

* **2011 Boot Block** — supports Sandy‑Bridge‑EP only
* **2013 Boot Block** — adds microarchitectural hooks Ivy‑Bridge‑EP requires

Nothing else on the PCB distinguishes a Rev 3 “v2” board from a Rev 1/2 “v1” board; the difference is firmware alone. Flashing those last 64 KiB therefore “converts” the board. HP BIOS updates *never* touch this region, so we do it manually via FPT once protections are down.

---

## 2  Intel ME & Manufacturing Mode

### 2.1 ME 7 vs ME 8
* ME 7 shipped with Sandy‑Bridge workstations.
* ME 8 accompanies Ivy‑Bridge and remains backward‑compatible with v1 CPUs.

Both reside in the same region; flashing ME 8 is optional but avoids POST errors on BIOS ≥ 3.88 where HP added a ME/CPU mismatch check. citeturn0file1

### 2.2 Manufacturing Mode ("ME Banner")
Placing FDO and E14 jumpers as instructed does two things:

1. Temporarily clears *Flash Descriptor locks* so FPT gains R/W to all regions.
2. Boots the PCH ME core in **Manufacturing Mode**, printing:
   > MANAGEMENT PLATFORM (ME) IN MANUFACTURING MODE

While in this mode the Boot Block is writable, but the recovery jumper is *ignored*, so we immediately exit (by restoring the original ME image) after flashing.

---

## 3  Board Jumpers & Headers

| Header | Location | Function | Default |
| ------ | -------- | -------- | ------- |
| **E1 ME/AMT FDO** | Near PCH heatsink | Lowers Flash‑Descriptor write‑protect bits | 1–2 (locked) |
| **E14 BB** | Top edge near VRMs | Gates Boot Block writes | open |
| **E15/16** | Near front panel | Crisis recovery to USB .bin | open |
| **SW1** | Behind CMOS coin cell | Momentary clear‑CMOS | n/a |

Photos are embedded in *docs/compatibility‑matrix.md* and the PDF guide. citeturn0file0

---

## 4  Software vs Hardware Flashing

| Method | Pros | Cons | When to Use |
| ------ | ---- | ---- | ----------- |
| **Intel FPT (software)** | Fast (≈ 30 s per region); no soldering | Requires functional Boot Block; typo bricks board; Rev 1/2 lack E14 | Rev 3 boards, routine mods |
| **CH341A + SOIC16 clip** | Works even on dead boards & Rev 1/2 | Slower; finicky clip; need disassembly | Brick recovery; initial 2013 BB on Rev 1/2 |

FPT cannot write the Boot Block unless both FDO and E14 are active **and** the host is in Manufacturing Mode.

---

## 5  Region‑Specific Flash Offsets (for FPT)

```
# Boot Block (64 KiB)
-A 0xFF0000 -L 0x010000

# BIOS region (10.5 MiB)
-A 0x580000 -L 0xA70000

# ME region (~5 MiB)
-ME  (shorthand switch)
```
These values are constant across all Zx20 variants.

---

## 6  Safety Checklist
* Confirm board **Revision 3** and presence of **E14 BB** jumper pins.
* Have a **v1 Xeon** installed for the unlock step.
* Use a **UPS** or at least avoid storms—power loss during erase = brick.
* Verify *every* dump with SHA‑256 on two different reads.
* Keep CH341A + clip ready in case something goes wrong.

Follow those rules and the risk of an unrecoverable board is very low (≈ 0.1 % incidence across community reports).

---

## 7  Further Reading & Sources
* **HashsumCollision – Z420/Z620/Z820 Boot Block Upgrade Guide v1.02** (PDF) citeturn0file0  
* **TechPowerUp Workstation Owners Club thread** – discovery of Manufacturing Mode path citeturn0file2  
* Win‑RAID forum – Intel ME System Tools & flash descriptor research

*End of theory document.*

