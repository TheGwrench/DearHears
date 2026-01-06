# Schneider Machine Expert 10-Year Archival Strategy

## Project Overview
- **Target Software:** EcoStruxure Machine Expert v2.2 / v2.3
- **Hardware:** Modicon Controllers
- **Objective:** 10-year "Time Capsule" (Zero internet, zero external dependencies)

---

## 1. Technical Implementation: The Gold Master VM
Docker is **not** compatible with the Windows-based drivers required for Machine Expert. A Virtual Machine (VM) is the industry standard for this use case.

### VM Configuration Specs
*   **Hypervisor:** [VMware Workstation Pro](www.vmware.com) (Now free for commercial use).
*   **Operating System:** Windows 10 Enterprise LTSC (Long-Term Servicing Channel).
*   **Disk Type:** **Fixed Size** (Minimum 80GB). *Crucial: Do not use "Dynamic/Thin Provisioning" to avoid bit-rot or header corruption over 10 years.*
*   **Network:** Two adapters: 
    1.  **Bridged** (To talk to physical PLCs).
    2.  **Host-Only** (Internal diagnostic).
*   **Licensing:** Use **Schneider License Manager** "Request/Response" workflow to perform an **Offline Activation**. This binds the license to the VM hardware ID permanently.

---

## 2. Archival Package (The "Everything" Folder)
Inside the VM, store a folder on the Desktop named `!!_RECOVERY_RESOURCES_!!` containing:

1.  **The Project Archive:** Save as `.projectarchive` (bundles all libraries/EDS/DTMs).
2.  **Offline Installer:** The full `.iso` of Machine Expert 2.2/2.3.
3.  **Activation Keys:** A `.txt` file containing the Activation IDs.
4.  **Hardware Drivers:** Manual installers for USB-to-PLC cables and Ethernet drivers.
5.  **Documentation:** PDF exports of the Logic Builder project and electrical schematics.

---

## 3. Bill of Materials (BOM) for 3-2-1 Backup
To prevent media failure, follow this list for physical archival:
# BILL OF MATERIALS: 10-YEAR ARCHIVAL KIT

* **Operating System:** Windows 10 Enterprise LTSC
    * **Cost:** ~$300
    * **Purpose:** Provides a permanent, stable OS base for the VM that won't force updates or expire.

* **Primary Storage:** [Samsung T7 Shield Rugged SSD (1TB)](www.samsung.com)
    * **Cost:** ~$130
    * **Purpose:** High-speed, durable storage for the active "Gold Master" VM file.

* **Archival Hardware:** [Pioneer External M-DISC Blu-ray Burner](usa.pioneer)
    * **Cost:** ~$150
    * **Purpose:** Hardware required to physically etch data into archival-grade discs.

* **Archival Media:** [Verbatim M-DISC BD-R (50GB)](www.verbatim.com)
    * **Cost:** ~$25
    * **Purpose:** The "Stone Tablet" copy; designed for a 1,000-year shelf life to prevent bit-rot.

* **VM Software:** [VMware Workstation Pro](www.vmware.com)
    * **Cost:** FREE
    * **Purpose:** The engine that runs the virtual environment and handles PLC USB drivers.

* **PLC Software:** EcoStruxure Machine Expert 2.2 / 2.3
    * **Requirement:** Must have the "Offline Medium" ISO file saved locally.

# SCHNEIDER MACHINE EXPERT: 10-YEAR ARCHIVAL STRATEGY

## 1. THE "GOLD MASTER" VIRTUAL MACHINE
* **Hypervisor:** [VMware Workstation Pro](www.vmware.com) (Ensures stable USB-to-PLC driver bridging).
* **Guest OS:** Windows 10 Enterprise LTSC (Avoids OS "feature bloat" and forced updates).
* **Storage Mode:** Fixed Disk (Non-Dynamic). Prevents file corruption over long-term storage.
* **Network Mode:** 
    * **Bridged Adapter:** For direct PLC/Controller communication.
    * **Host-Only Adapter:** For isolated local diagnostics.

## 2. OFFLINE LICENSING WORKFLOW
1. Launch **Schneider License Manager** inside the air-gapped VM.
2. Select **Activate > By Web Portal / Offline**.
3. Generate the **Request XML** file and save it to the host.
4. Upload that file to the Schneider License Portal from an internet-connected PC.
5. Download the **Response XML** and import it back into the VM.
    * **Result:** The license is permanently bound to the VM hardware ID without requiring future pings to Schneider's servers.

## 3. INTERNAL RECOVERY ASSETS (Stored on VM Desktop)
Store a folder named `!!_RECOVERY_RESOURCES_!!` containing:
* **The Project Archive:** Save via `File > Project Archive > Save Archive` (.projectarchive).
* **The Installer:** Full "Offline Medium" `.iso` for Machine Expert 2.2 / 2.3.
* **Activation Codes:** A `.txt` file containing all Product IDs and Serial Numbers.
* **Support Files:** PDF versions of electrical schematics, I/O maps, and DTM/EDS catalogs.

## 4. THE 10-YEAR "ACID TEST"
1. **Air-Gap:** Disconnect the physical host machine from all networks.
2. **Transfer:** Move the VM file from your M-DISC or SSD to a clean laptop.
3. **Launch:** Boot the VM and confirm Machine Expert shows as "Licensed."
4. **Compile:** Open the `.projectarchive` and execute **Clean All > Build All**.
5. **Success:** If the code compiles to a binary without internet access, the capsule is secure for the next decade.

---
*Created January 2026 for Long-Term Industrial Asset Management.*

