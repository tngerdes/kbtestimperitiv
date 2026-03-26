---
title: "SOP: Backup Nonstandard Directories to OneDrive for Business"
category: "SOPs"
source: "SOP - Backup Nonstandard Directories to OneDrive.docx"
---

# SOP: Backup Nonstandard Directories to OneDrive for Business

This SOP provides PowerShell commands and guidance for backing up non-standard directories (outside of Desktop, Documents, and Pictures) to OneDrive for Business using `Copy-Item`.

---

## General Code Templates

### Back up a directory in the user profile:

```powershell
Copy-Item -Path C:\Users\$env:USERNAME\directory* -Recurse -Destination C:\Users\$env:USERNAME\"OneDrive - {{COMPANY}}"\backupdirectory\
```

### Back up a directory from Program Files:

```powershell
Copy-Item -Path C:\"Program Files (x86)"\directory* -Recurse -Destination C:\Users\$env:USERNAME\"OneDrive - {{COMPANY}}"\backupdirectory\
```

---

## Important Notes

- **Spaces in directory names** — Any directory with spaces in its name must be enclosed in quotation marks.
- **Root-level backups** — You can back up folders to the root of OneDrive (not inside Desktop/Documents/Pictures) to help protect users from accidentally deleting backed-up data.
- **`-Recurse` flag** — This flag is necessary to ensure all subfolders and files are copied. Do not omit it.
- **`{{COMPANY}}` placeholder** — This is **not** a valid flag. You must manually determine the exact name of the client's OneDrive folder and put it in quotation marks. For example:
  ```
  "OneDrive - Imperitiv Solutions"
  ```
- **`$env:USERNAME`** — This flag pulls the currently logged-on user. This script **will not work** if you need to back up files stored in one user's directory to a different user's OneDrive.
