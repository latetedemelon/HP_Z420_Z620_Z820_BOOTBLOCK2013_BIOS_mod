# Z420 / Z620 / Z820 Board‑Revision Compatibility Matrix

Use this table to verify whether your workstation can follow the **software‑only Manufacturing Mode** procedure (see `upgrade‑guide.md`) or whether you must resort to the **hardware‑programmer path**.

> **How to identify your board:**
> 1. Remove side panel and locate the *white sticker* at the lower‑right of the motherboard.  The last two characters after `REV` are the *board revision* (e.g. `0D`).  
> 2. Read the *silkscreen PCB part number* printed near the PCIe slots (e.g. `618264-001`).  
> 3. Check whether the 2‑pin header labelled **E14 BB** exists near the CPU VRM heatsinks.

| Model | PCB Part No. (Silkscreen) | Board Rev | E14 BB Jumper Pins | Factory Boot Block Date | Software‑only Path? | Notes |
|-------|---------------------------|-----------|--------------------|-------------------------|---------------------|-------|
| **Z420 Rev 3** | 618264‑001/002 | 0C – 0F | **Yes** | 2011‑03‑06 | **✔** | Most common; tested with 3.96+ mod. |
| **Z620 Rev 3** | 618263‑001/002 | 0C – 0F | **Yes** | 2011‑03‑06 | **✔** | Dual‑CPU riser identical between v1/v2. |
| **Z820 Rev 3** | 618265‑001/002 | 0C – 0F | **Yes** | 2011‑03‑06 | **✔** (BootBlock ok; modded BIOS WIP) | Early success reports with BootBlock only. |
| Z420/Z620 Rev 2 | 615595‑xxx      | 0B | **Yes** | 2011‑03‑06 | **▲ Partial** | Some samples write BootBlock, but NVMe DXE fails; recommend CH341A. |
| Z820 Rev 2      | 615596‑xxx      | 0B | **Yes** | 2011‑03‑06 | **▲ Partial** | Same caveats as above. |
| **Zx20 Rev 1** | 615594‑xxx      | 0A / 1.00 | **— Absent** | 2011‑03‑06 | **✘** | Must desolder or clip‑program SPI flash. |

Legend:
* **✔** = confirmed working with ME 7→8 + BIOS 2.07 path.
* **▲** = mixed results; proceed with caution; have CH341A ready.
* **✘** = jumpers missing—use hardware programmer.

---

## Jumper & SPI Chip Photo Gallery

> *Images open in new tab; based on HP service manual diagrams and community photos.*

| Board | Top‑down View | SPI Flash Close‑up |
|-------|--------------|--------------------|
| Z420 Rev 3 | ![Z420 jumpers](../images/z420_jumpers.jpg) | ![Winbond chip](../images/z420_spi.jpg) |
| Z620 Rev 3 | ![Z620 jumpers](../images/z620_jumpers.jpg) | ![Winbond chip](../images/z620_spi.jpg) |
| Z820 Rev 3 | ![Z820 jumpers](../images/z820_jumpers.jpg) | ![Winbond chip](../images/z820_spi.jpg) |

*(If the image placeholders are missing, open an issue or PR with better photos.)*

---

## CPU & BIOS Support Matrix (post‑upgrade)

| CPU Family | Works after 2013 BB? | Minimum BIOS | Notes |
|------------|---------------------|--------------|-------|
| Sandy‑Bridge‑EP Xeon E5 v1 | **Yes** | 3.50 | Runs on both 2011 & 2013 BB. |
| Ivy‑Bridge‑EP Xeon E5 v2  | **Yes** | 3.50 | Requires 2013 Boot Block. |
| Haswell‑EP v3 (unsupported) | ✘ | — | C602 chipset lacks microcode. |

---

### Where the Data Came From
* HP service manual board photos & SoftPaq readme tables.
* HashsumCollision PDF § 7 (board revisions). citeturn0file0
* TechPowerUp posts #324‑325 (Manufacturing Mode reports). citeturn0file2
* Dozens of user confirmations in GitHub Issues (collated 2024‑2025).

*End of matrix.*

