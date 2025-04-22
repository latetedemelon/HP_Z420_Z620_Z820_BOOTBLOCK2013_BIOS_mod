# HP Zx20 Boot Block 2013 & Modded BIOS **Upgrade Guide**

> **Models covered:** HP Z420 / Z620 / Z820 **Revision 3** (‘v1’ Sandy‑Bridge boards with the E14 BB jumper). If your PCB is Rev 1 or 2, or the jumper is missing, follow the *Hardware‑Programmer* path in § 8.
>
> **Spare CPU required:** You **must** have a *v1* Sandy Bridge Xeon for the unlock step—BIOS 2.07 will not POST on *v2* CPUs.
>
> **Brick‑proofing:** Make a verified 16 MiB backup **before** writing anything and keep a **SOIC‑16 test‑clip + CH341A** on hand.  Recovery only takes a few minutes if things go sideways.

---

## 0  Workflow at‑a‑Glance
1. **Build a bootable FreeDOS USB** containing the firmware files & Intel tools.
2. **Back‑up** the entire SPI flash with Intel FPT.
3. **Enter Manufacturing Mode** (ME banner) so the Boot Block becomes writable.
4. *(Optional but recommended)* **Write the 2013 Boot Block**.
5. **Flash the modded BIOS region** you picked from *Releases*.
6. **Restore ME & GbE regions**, exit Manufacturing Mode, clear CMOS.
7. Boot with the v2 Xeon, verify BIOS v3.96+/Boot Block 2013, enjoy NVMe + ReBAR.

Estimated bench‑time once the USB is ready: **≈ 30 min**.

---

## 1  Gather the Tools & Files

| Item | Purpose |
| ---- | ------- |
| 2–8 GB USB 2.0 stick | DOS boot & tool storage |
| **Rufus** (or HP USB Format Tool) | Write a bootable **FreeDOS 1.4** image |
| **IMET8.zip** (in repo > *Releases*) | Intel FPT toolkit + convenient batch files |
| Folder `J620` / `J420` / `J820` (from *Releases*) | Stock & modded ROMs for your board |
| **Tweezers** (long, locking) | Safe jumper manipulation inside cramped chassis |
| **SOIC‑16 clip + CH341A** | Emergency offline flash recovery |

### 1.1 Build the FreeDOS USB

#### Option A (Windows) – **Rufus**
1. Download **Rufus** ≥ 4.4.
2. Insert the USB stick → Rufus detects it automatically.
3. **Boot selection → FreeDOS** (Rufus ships the image)
4. **Partition scheme → MBR**, **File system → FAT32** (default values)
5. Press **START** → wait until Rufus shows *READY*.

&nbsp;

#### Option B (Windows / macOS / Linux) – **balenaEtcher**
1. Download **balenaEtcher** (portable AppImage, dmg, or exe).
2. Grab the official **FreeDOS 1.4 "FullUSB" .img** from freedos.org.
3. Launch Etcher → **Flash from file** → select the `.img` → choose the USB drive.
4. Click **Flash!** and wait for completion.

Etcher is simpler but does **not** embed FreeDOS—hence the extra step of fetching the `.img`.

---

After the tool finishes, reopen the drive in your OS and copy these items to the **root**:
```
IMET8\           (folder)
MEBX20\          (folder – ME blaster)
J207\            (official BIOS 2.07)
BB2013\          (Boot Block binaries)
CJ6Y.BIN         (or CJ8Y.BIN etc – your chosen modded BIOS)
BACKUPS\         (empty folder; FPT dumps land here)
```
*Tip:* Remove folders for boards you don’t own to avoid accidental flashes.

### 1.2 Locate the Jumpers Locate the Jumpers
* E14 **BB** – enables Boot Block writes  
* E1 **FDO/ME** – disables flash descriptor locks  
* Momentary **CMOS clr** button  
Refer to the photo diagrams in *docs/theory.md*.

---

## 2  Back‑up Your Current Firmware
1. Move **FDO** jumper to the *write‑enable* pins; leave E14 BB **open** for now.
2. Boot the FreeDOS stick → `C:\>` prompt.
3. Run:
   ```
   cd IMET8
   backup 00        :: creates BACKUPS\FIRM00.BIN (16 MiB)
   md5all 00        :: optional integrity check
   ```
4. Copy `FIRM00.BIN` off the USB to another PC *and* the cloud.

---

## 3  Unlock Manufacturing Mode
1. On the **powered‑off** workstation:
   * Place a jumper on **E14 BB**.
   * Keep **FDO** in the write‑enable position.
2. Power ON, boot FreeDOS, then:
   ```
   cd MEBX20
   MEBLAST J6Y_0396.bin   :: Z420/Z620  (use J8Y_0396.bin for Z820)
   ```
3. Immediately flash the **official BIOS 2.07**:
   ```
   cd \J207
   DOSFlash.exe
   ```
4. Press **Ctrl + Alt + Del** → The system reboots twice. You should now see the banner:
   > MANAGEMENT PLATFORM (ME) IN MANUFACTURING MODE


---

## 4  Write the 2013 Boot Block (once)
> **Skip only** if your board *already* shows Boot Block 2013 (File → System Info).

```dos
cd \BB2013
fpt.exe -A 0xFF0000 -L 0x010000 -F B13V620.bin   :: change 6→4 or 8 for your board
```
Expect ≈ 30 s. Do **not** interrupt power.

---

## 5  Flash the Modded BIOS Region
1. Pick the cropped file that matches your board & microcode choice, e.g. `cJ6Y_0396_NRE_mc96p.bin`.  Rename to **CJ6Y.BIN**.
2. Flash only the BIOS region:
   ```dos
   fpt.exe -A 0x580000 -L 0xA70000 -F CJ6Y.BIN
   ```

---

## 6  Exit Manufacturing Mode & Clean‑Up
```dos
cd IMET8
fpt.exe -ME  -F MEOO11.bin   :: restore original ME region
fpt.exe -GBE -F GBEO11.bin   :: restore GbE region
```
Power off → remove **both** jumpers → press **CMOS clear** for 10 s → reconnect AC.

Boot to BIOS (F10) → File → System Information:
* *Boot Block Date = 03/06/2013* ✔
* BIOS 3.96/3.96+ ✔
* ME 8.x ✔ (optional but recommended)

Now shut down, install your **v2 Xeon**, and enjoy.

---

## 7  Flash Commands – Quick Reference
```
fpt.exe -D BACKUP.BIN                 :: full‑chip dump
fpt.exe -ME  -F ME8_396i.BIN          :: write only the ME region
fpt.exe -A 0xFF0000 -L 0x010000 -F BB.bin  :: Boot Block
fpt.exe -A 0x580000 -L 0xA70000 -F BIOS.bin :: BIOS region
```

---

## 8  Hardware‑Programmer Path (Rev 1/2 boards or brick recovery)
1. Solder or clamp a **SOIC‑16 clip** to the SPI flash (Winbond W25Q128 etc.).
2. Use `flashrom` with a **CH341A** or **Raspberry Pi** to:
   * dump → edit Boot Block & ME in HxD/UEFITool → verify checksums
   * write → verify → reinstall board → clear CMOS.
3. Re‑join this guide at **§ 6**.

Detailed pinouts, clip photos, and `flashrom` commands live in *docs/troubleshooting.md*.

---

## 9  What’s Next?
* **Enable NVMe boot** – simply plug your PCIe SSD, create a GPT OS disk, and it shows up in UEFI boot order.
* **Toggle Re‑Size BAR** – BIOS → Advanced → PCI settings.
* **Upgrade ME v7→v8** if you skipped it earlier – instructions in *theory.md* appendix.

---
