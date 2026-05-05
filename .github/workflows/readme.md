# Image Processing & Mutation System

This repository uses a **file-based command system** to manage images via GitHub Actions.

All operations are triggered by placing JSON files into the:

```
/delete-queue/
```

folder.

---

# 🚀 How It Works

1. Create a batch file inside `/delete-queue/`
2. Push it to the repository
3. GitHub Actions processes the file
4. Changes are applied to:

   * `/images/` (actual files)
   * `/images/files.json` (metadata)

---

# 📁 File Structure

```
/images/
  ├── *.webp
  └── files.json

/delete-queue/
  └── batch-*.json
```

---

# 🧾 Batch File Format

All commands use this structure:

```json
{
  "changes": [
    { "action": "...", ... }
  ]
}
```

---

# 🔥 Supported Actions

---

## 🗑 DELETE

Removes an image and its entry from `files.json`.

```json
{
  "changes": [
    { "action": "delete", "id": "1776581395549" }
  ]
}
```

---

## ✏️ UPDATE

Updates a specific attribute in `files.json`.

```json
{
  "changes": [
    {
      "action": "update",
      "id": "1776584939865",
      "attribute": "meta",
      "value": {
        "featured": true,
        "category": "kitchen"
      }
    }
  ]
}
```

---

## 🔁 RENAME

Renames the actual file AND updates `files.json`.

```json
{
  "changes": [
    {
      "action": "rename",
      "id": "1776581395549",
      "value": "new-image-name.webp"
    }
  ]
}
```

---

# ⚡ Example: Multiple Actions

```json
{
  "changes": [
    { "action": "delete", "id": "1776580023116" },
    {
      "action": "update",
      "id": "1776584939865",
      "attribute": "meta",
      "value": { "priority": 1 }
    },
    {
      "action": "rename",
      "id": "1776581395549",
      "value": "hero-image.webp"
    }
  ]
}
```

---

# 🧠 Important Notes

* All operations are **ID-based**
* IDs map to filenames like:

  ```
  1776581395549.webp
  ```
* Some files may include suffixes:

  ```
  1776663177208-xyz.webp
  ```

  These are still matched by ID

---

# ⚠️ Warnings

* Actions are **destructive**
* Deleting images removes them permanently from the repo
* Renaming affects file paths and URLs

---

# 🧪 Debugging

After execution, the workflow:

* Updates `files.json`
* Removes processed batch files
* Commits changes automatically

If something goes wrong:

```bash
git revert HEAD
```

---

# 🧩 Design Philosophy

This system is:

* File-driven (no database)
* Deterministic (no guessing)
* Git-native (everything versioned)

---

# 🧾 Summary

| Action | Effect                          |
| ------ | ------------------------------- |
| delete | removes image + metadata        |
| update | modifies metadata               |
| rename | renames file + updates metadata |

---

If something breaks, it’s probably because:

* `files.json` is out of sync
* ID doesn’t match filename
* or someone got a little too confident with batch deletes

---
