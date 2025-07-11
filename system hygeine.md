### 🖥️ What the treemap is telling you

| Color-block (rough label)                                                       | Typical contents                               | Approx. size on your map                                                     | Keep / Clean-up notes                                                                                                                     |
| ------------------------------------------------------------------------------- | ---------------------------------------------- | ---------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Huge yellow square (left)**`Users\<name>\AppData\Local\Temp` & browser caches | ~ 10 GB                                        | **Clean** – safe to empty Temp & browser caches regularly.                   |                                                                                                                                           |
| **Green block (center-bottom)**`hiberfil.sys` (hibernation file)                | 6–8 GB                                         | **Optional** – disable hibernation (`powercfg /hibernate off`) to remove it. |                                                                                                                                           |
| **Purple/orange clusters (Program Files + Windows)**                            | Installed apps, Windows system files           | Varies                                                                       | **Leave** – only uninstall apps you recognise & don’t need.                                                                               |
| **Pink rectangle (pagefile.sys)**                                               | Virtual-memory swap file                       | 2–4 GB                                                                       | **Leave** – Windows manages this; shrink only if disk is very tight.                                                                      |
| **Dark-green & teal clusters (Docker, npm, pip caches)**                        | Container images, node _modules, Python wheels | 3–5 GB combined                                                              | **Clean** – prune unused Docker images, run `npm cache clean --force`, `pip cache purge`.                                                 |
| **Orange patchwork (Windows\Installer)**                                        | Old MSI installer caches                       | 3–4 GB                                                                       | **Partially clean** – use “Disk Cleanup → Clean up system files → Windows Update Cleanup” or PatchCleaner (free) to remove orphaned MSIs. |
| **Small magenta/gold squares inside projects**                                  | VSCode, IntelliJ, build artifacts              | < 1 GB each                                                                  | **Targeted** – nuke “bin/obj/.gradle” or “.angular/cache” folders per project.                                                            |

_(Exact paths may differ; click any rectangle in WinDirStat to see the real folder.)_

---

### Quick wins (≈ 15 GB back)

1. **Empty Temp & browser caches**  
    `Run → %TEMP% → Ctrl-A + Delete` (skip files in use).
    
2. **Disable hibernation** (if you never use “Hibernate”)
    
    ```cmd
    powercfg /hibernate off
    ```
    
3. **Prune Docker** (if you develop with containers)
    
    ```bash
    docker system prune -a
    ```
    
4. **Clear Node & Python caches**
    
    ```bash
    npm cache clean --force
    pip cache purge
    ```
    
5. **Disk Cleanup (system files)**
    
    - Press **Win** + `cleanmgr` → **Clean up system files**.
        
    - Check **Windows Update Cleanup** & **Delivery Optimization**.
        

---

### Medium wins (5-10 GB)

|Task|Tool / Command|Caution|
|---|---|---|
|Uninstall unused desktop apps & games|Settings → Apps|Leave GPU drivers, Visual C++ redists.|
|Remove old VS build artifacts|Delete `bin/`, `obj/`, `.gradle/`, `.angular/cache` in finished projects|Regenerate on next build if needed.|
|Trim npm project dependencies|`npm prune --production` in deployed projects|Run after `npm install` to strip devDeps.|

---

### Long-term hygiene

1. **Turn on Storage Sense** (Settings → System → Storage) to auto-purge Recycle Bin & Temp.
    
2. **Keep large assets off C:** move videos, VMs, ISO images to an external / secondary drive.
    
3. **Snapshot WinDirStat monthly** – track new space hogs before C: fills again.
    
4. **Automate cache cleanup** with a weekly PowerShell script or a tool like BleachBit (portable).
    

---

### When space is still tight

- **Shrink WinSxS safely**:
    
    ```cmd
    dism /online /cleanup-image /startcomponentcleanup
    ```
    
- **Move OneDrive/Dropbox sync folder** off the system drive.
    
- **Add a secondary SSD/HDD** and relocate Docker, npm, and Python package caches via environment vars (`DOCKER_ROOT`, `npm config set cache`, `pip --cache-dir`).
    

Follow the quick-win list first—you’ll likely free 12-20 GB in under 30 minutes without touching anything critical.