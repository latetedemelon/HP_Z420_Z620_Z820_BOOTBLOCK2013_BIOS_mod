# HP Z420 / Z620 / Z820 Boot Block 2013 Upgrade & Modded BIOS Project

Welcome! This repository documents—and provides the tools for—transforming **v1** HP Zx20 workstations (Z420, Z620, Z820) into their **v2‑capable** counterparts by:

* flashing the 2013 Boot Block so Ivy Bridge‑EP Xeons are recognised, and
* optionally installing a fully‑patched BIOS that adds NVMe boot, Re‑Size BAR, and alternate microcodes.

If you just want to get the job done, jump straight to the **[Step‑by‑Step Guide](docs/upgrade‑guide.md)**.  The rest of this README gives you the big picture: what you gain, whether your board is compatible, who made all this possible, and quick answers to common questions.

---

## 1. Why Upgrade?

| Feature                                    | Before (2011 BB) | After (2013 BB + Mod) |
| ------------------------------------------ | ---------------- | --------------------- |
| CPU support                                | Sandy Bridge‑EP (v1) only | **+ Ivy Bridge‑EP (v2)** – up to 12C/24T |
| PCIe NVMe boot                             | ❌ require Clover/DUET | **✔ Native UEFI NVMe** |
| Re‑Size BAR / Large BAR for GPUs           | ❌ | **✔** |
| Microcode choice                           | Latest only      | Choose latest *or* pre‑2018 for OC tests |
| RAM speeds                                 | Up to DDR3‑1600  | Up to DDR3‑1866 (with v2 CPUs) |

*Net gain: up to **+50 % multi‑core** performance and modern storage/GPU features on decade‑old iron.*

## 2. Scope & Compatibility

| Model  | Board silkscreen | BootBlock jumper (E14 BB) | Status |
| ------ | ---------------- | ------------------------- | ------ |
| Z420 Rev 3 | 618264‑xxx | Yes | ✔ Fully verified |
| Z620 Rev 3 | 618263‑xxx | Yes | ✔ Fully verified |
| Z820 Rev 3 | 618265‑xxx | Yes | ● BootBlock OK – BIOS mods WIP |
| Zx20 Rev 1/2 | 615594‑xxx etc. | **No jumper** | ✘ *Use hardware flasher or buy a Rev 3 board* |

> **Up‑front CPU requirement:** You **must** have a spare **v1 Sandy Bridge Xeon** for the unlock step—BIOS 2.07 will not POST on v2 CPUs.  Swap back to the v2 once the mod is complete.

See **[docs/compatibility‑matrix.md](docs/compatibility‑matrix.md)** for finer PCB/Sticker revision details.

## 3. Credits & Acknowledgements

Project maintainers & major contributors:

* **@latetedemelon** – documentation rewrite
* **@SuperThunder** – comprehensive hardware flashing guide
* **@silentbogo** – first proof‑of‑concept mod (TechPowerUp forums)
* **@BillDH2k** – extensive field testing & documentation
* Tooling by **LongSoft (UEFITool/UEFIShell)**, **MMTool A4**, and the Win‑RAID community

Community knowledge distilled from:

* *Z620/Z820 Boot Block* megathreads on HP Support & TechPowerUp, mirrored in **[docs/history.md](docs/history.md)**
* HashsumCollision’s **[BIOS Boot Block Upgrade Guide* (PDF)](https://github.com/SuperThunder/HP_Z420_Z620_Z820_BootBlock_Upgrade)** – theory sections referenced throughout

---

## Documentation Library

| Document | Purpose |
| -------- | ------- |
| **[upgrade‑guide.md](docs/upgrade‑guide.md)** | Hands‑on step‑by‑step instructions (USB build, jumpers, commands) |
| **[theory.md](docs/theory.md)** | Deep dive into SPI layout, ME firmware, Boot Block internals |
| **[history.md](docs/history.md)** | Timeline of discoveries, forum reports, and SoftPaq links |
| **[compatibility‑matrix.md](docs/compatibility‑matrix.md)** | Board rev, BIOS rev, jumper presence, known quirks |
| **[troubleshooting.md](docs/troubleshooting.md)** | Common errors, recovery with SOIC clip + CH341A |

---

## Frequently Asked Questions

**Q 1 – Can I perform every step with a v2 Xeon installed?**  No. BIOS 2.07 (needed for Manufacturing Mode) will not POST on v2 CPUs. Install any v1 E5‑16xx/26xx first.

**Q 2 – Do I really need to update the Boot Block?**  Yes if you want Ivy Bridge‑EP CPUs; optional if you only need NVMe/ReBAR and will stay on v1 silicon.

**Q 3 – What if something goes wrong?**  A verified full‑chip backup plus a **SOIC16 clip & CH341A programmer** can restore the board in minutes. See *troubleshooting.md*.

**Q 4 – Which modded BIOS flavour should I choose?**  • Z420/Z620 single‑socket → 3.91+ or 3.96+.  • Z820 dual‑socket → stick to 3.96/3.96+.  Microcode choice (`mc91 vs mc96`) only affects Spectre/Meltdown mitigations.

**Q 5 – How long does the whole process take?**  Around 30 min once you have the USB prepared and jumpers accessible.

---

*Ready?  Head over to **[docs/upgrade‑guide.md](docs/upgrade‑guide.md)** and make your USB stick.*

