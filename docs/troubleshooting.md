Below is the full **`docs/troubleshooting.md`** text.  
Copy‑paste it into your repository as‑is (no front‑matter needed).

---

# Troubleshooting Guide  
_HP Z420 / Z620 / Z820 Boot‑Block 2013 Upgrade_

> Use this guide when something does **not** go according to the main upgrade workflow.  
> Problems are grouped by the moment they typically surface, followed by “generic” fixes and a last‑chance recovery checklist.

---

## 0  Quick‑Reference Table

| Symptom | Most‑likely cause | Where to start |
|---------|------------------|----------------|
| **USB stick will not boot** | USB not set first, FreeDOS image bad, UEFI secure‑boot on | § 1.1 |
| **`flashrom` cannot detect chip** | Bad clip contact, wrong pin‑out, board still powered | § 2 |
| **Write succeeds but verify fails** | Weak 3 V3 rail, passive drain from board | § 2.4 |
| **Board powers on but no POST beeps / codes** | Corrupted boot‑block region | § 3 |
| **“ME in Manufacturing Mode” every boot** | ME region was touched / header left bridged | § 4 |
| **Only one NIC appears / MAC duplicates** | GbE addresses overwritten | § 5 |
| **v2 Xeon shows 0 × 000000000 (CPUID 0) or reboots** | Still on ME 7 or BIOS < 3.50 | § 6 |
| **`FPT` says “locked flash region”** | E14 / E1 jumper not set, early PCB revision lacks E14 | § 2.5 |

---

## 1  Bootable USB Issues

### 1.1 USB stick is ignored, boots to OS/HDD  
* Re‑enter BIOS → **Storage → Boot order**: place the stick first, _disable UEFI_ and _Secure Boot_.  
* Verify the stick is **FAT32** and boots in another PC.  
* **Rufus path:** select *“FreeDOS (no GUI)”* template.  
* **balenaEtcher path:** flash the `FD13FULL.img` from FreeDOS 1.3; confirm SHA‑256 matches `c6583957…`.

### 1.2 FreeDOS starts but update tools crash  
* Use the **“Safe Mode (no HIMEM XMS)”** option.  
* Re‑copy HP SoftPaq files; they corrupt if copied while mounted read‑only in Windows.

---

## 2  Programmer / Clip Problems

### 2.1 `flashrom` prints “No EEPROM/flash device found”
1. Double‑check the rainbow‑cable mapping with a multimeter—pin 1 on **SOIC‑16 clip → pin 1** on the DIP board.  
2. Reduce SPI speed:  
```bash
flashrom -p linux_spi:dev=/dev/spidev0.0,spispeed=512
```  
   If it now detects, leave `512 kHz` for all operations.

### 2.2 Inconsistent hashes between successive dumps  
* 9 / 10 times the clip is just **slightly tilted**.  
* Clean the chip legs with isopropyl.  
* Support the cable—any pull releases pressure on the pins.

### 2.3 Writes appear successful, verify immediately fails  
* The 3 V3 from Pi / CH341A is sagging under motherboard load.  
  * Unplug the ATX 24‑pin from the workstation (removes passive drain).  
  * Or inject a bench 3 V3 rail (tie all grounds together).

### 2.4 Early PCB (no E14) – software path refuses to unlock  
* You **must** use the hardware path (clip/CH341A) on those boards.  
* Board‐ID list in _compatibility‑matrix.md_.

### 2.5 `FPT` “locked region” after jumpers moved  
* Power‑cycle the workstation _after_ setting E1 + E14; CMOS battery out for 30 s if still locked.

---

## 3  No POST or repeating beep codes after flash

1. Clear CMOS (jumper JCMOS + remove power).  
2. If still dead: re‑flash the **original untouched dump** with the programmer.  
3. Verify the SHA‑256 of the file on the stick matches the one you flashed earlier.  
4. Common editing errors:  
   * Boot‑block pasted at the wrong offset (should start at `0xFF0000`)  
   * File saved as **UTF‑8‑with‑BOM**—always keep it **raw binary**.

---

## 4  Stuck in “ME in Manufacturing Mode”

* Boot with E1 jumper _bridged_, E14 _open_; run:  
  ```bash
  fpt -greset
  ```  
  immediately followed by power‑off, then place E1 back to normal.  
* If on ME 7, consider finishing the ME 8 upgrade (see § 6).

---

## 5  NIC MAC address issues

The two onboard NICs use **three** stored MACs (see addresses in PDF) citeturn0file0.  
If only one NIC shows or both share an address:

1. Read a **good** dump from another identical board _or_ copy the original MACs you recorded.  
2. Open your current flash in a hex‑editor, patch the GbE region (first at `0x001000`).  
3. Program only the 8 kB GbE region:  
   ```bash
   flashrom -p linux_spi:dev=/dev/spidev0.0 \
            -l gbe.layout -i gbe -w fixed_mac.bin
   ```  
   where `gbe.layout` maps `00001000:00002FFF gbe`.

---

## 6  v2 Xeon boots / reboots loop

| Check | Fix |
|-------|-----|
| Boot‑block date still 2011 | Re‑do the clip flash; verify in BIOS ‑> System Info (should read **03/06/2013**). |
| BIOS version < 3.50 | Update to 3.85 from FreeDOS. |
| ME still v7, BIOS ≥ 3.88 | Flash ME 8 using the SoftPaq **SP82684** (DOS tools) or patch ME region in a dump and re‑flash. |
| AMT enabled while ME v7 + v2 CPU | Disable AMT, or move E1 jumper to force ME off (see test‑notes) citeturn0file1. |

---

## 7  Clip won’t fit, space too tight

* Use short **tweezer‑style IC hooks** on each leg (takes longer, but works with the PSU installed).  
* Or desolder just the Vcc pin and lift it enough for an 8‑pin SOIC‑8 clip adapter.

---

## 8  Last‑Chance Recovery Checklist

1. Programmer + SOIC‑16 clip known‑good on a loose flash chip.  
2. Verified original dump on two separate USB sticks.  
3. External 3 V3 supply delivering ≥ 150 mA.  
4. Board completely isolated from PSU.  
5. Re‑flash original → verify → re‑install → CMOS clear → power‑on.

If it still fails, the chip itself may be damaged—replace with a blank W25Q128BV and program your good dump.

---

## 9  Useful Links

* Upgrade‑guide.md § numbers referenced above  
* PDF Boot‑Block Upgrade Guide (rev 1.02) citeturn0file0  
* HP forum threads (Manufacturing Mode & ME flash) citeturn0file2

---

**End of file**
