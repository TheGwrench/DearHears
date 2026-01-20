# SD Logger: Persistent Variables & File Write Architecture

## Problem Statement

Machine Expert requires `PERSISTENT` variables to exist in a dedicated **Persistent Variables list object** at application level, not inside task POUs.

Build warning C0569 indicates:
```
No VAR_PERSISTENT list is part of the application to enter instance path for variable LOG_ToSD.sLine
```

## Why Persistent Variables Matter

`SysFileWrite()` accepts a pointer parameter (`pbyBuffer := ADR(...)`). This pointer must reference memory that survives across task cycles. Local stack variables deallocate after each cycle, invalidating the pointer.

**Result of using local variables:** Undefined behavior, silent data corruption, or runtime crash on next file write attempt.

## Solution: Two-Bucket Architecture

### Bucket 1: Local Variable (Fast Build)
- **Location:** Inside LOG_ToSD task VAR block
- **Purpose:** Fast CSV string construction using CONCAT
- **Lifetime:** Rebuilt fresh each loop iteration, discarded after write
- **Speed:** Fast (no persistent memory overhead)

```structured_text
VAR
    sCsvLine : STRING(256);  // Work bucket — temporary, rebuilt each cycle
END_VAR
```

### Bucket 2: Persistent Variable (Safe Storage)
- **Location:** Dedicated Persistent Variables list at application level
- **Purpose:** Pointer target for SysFileWrite() — guaranteed to survive cycles
- **Lifetime:** Persists across power cycles and downloads
- **Access:** Global scope, referenced as `PersistentVars.sCsvLinePersist`

```structured_text
VAR_GLOBAL PERSISTENT
    sCsvLinePersist : STRING(256);  // Storage bucket — persistent memory
    uiCsvLineLen : DWORD := 46;     // Line length for SysFileWrite ulSize parameter
END_VAR
```

## Implementation Steps

### 1. Create Persistent Variables List
- Applications Tree → right-click **MyController.Application**
- Select **Add Other Objects** → **Persistent Variables**
- Name it `PersistentVars`

### 2. Add Variables to PersistentVars
Inside the new object, declare:
```structured_text
VAR_GLOBAL PERSISTENT
    sCsvLinePersist : STRING(256);
    uiCsvLineLen : DWORD := 46;
END_VAR
```

### 3. Update LOG_ToSD Task
Declare local work variable:
```structured_text
VAR
    sCsvLine : STRING(256);  // Local — fast CSV line builder
    stEntry : /* your struct */;
    i : INT;
END_VAR
```

### 4. CSV Build & Write Pattern
```structured_text
FOR i := 0 TO 226 DO
    (* Build CSV line in local variable *)
    sCsvLine := DWORD_TO_STRING(stEntry.Timestamp);
    sCsvLine := CONCAT(sCsvLine, ',0x');
    sCsvLine := CONCAT(sCsvLine, DWORD_TO_STRING(stEntry.CobID));
    sCsvLine := CONCAT(sCsvLine, ',');
    (* ... add data bytes ... *)
    sCsvLine := CONCAT(sCsvLine, '$N');
    
    (* Copy to persistent before write *)
    PersistentVars.sCsvLinePersist := sCsvLine;
    PersistentVars.uiCsvLineLen := INT_TO_DWORD(LEN(sCsvLine));
    
    (* Write from persistent — pointer guaranteed valid *)
    SysFileWrite(
        hFile := hFile,
        pbyBuffer := ADR(PersistentVars.sCsvLinePersist),
        ulSize := PersistentVars.uiCsvLineLen,
        pResult := ADR(fileResult)
    );
END_FOR;
```

## Key Points

| Aspect | Local `sCsvLine` | Persistent `sCsvLinePersist` |
|--------|------------------|------------------------------|
| **Declaration** | Inside LOG_ToSD task VAR | In PersistentVars application-level object |
| **Scope** | Local to task | Global |
| **Lifetime** | Rebuilt each cycle | Survives cycles/restarts |
| **Speed** | Fast | Slower (persistent memory I/O) |
| **Purpose** | Work buffer (CONCAT operations) | SysFileWrite pointer target |
| **Persistence** | No | Yes (PERSISTENT keyword) |

## Build Verification

After implementation, rebuild project:
- **Expected result:** 0 errors, no C0569 warnings for these variables
- **If warnings persist:** Verify PersistentVars object exists at application level (not inside task)

## Reference
- SE Documentation: Persistent Variables require dedicated list object at application level
- SysFileWrite parameter `pbyBuffer` must reference valid persistent memory address
- ADR() function returns memory address of variable — must survive across task cycles
