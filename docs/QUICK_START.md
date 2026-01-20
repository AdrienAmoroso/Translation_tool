# Quick Start - Translation Tool

## 3 Steps to Translate

```
1. Double-click:  run_translation.bat
2. Watch:        Progress bars appear
3. Done:         Check Excel for translations
```

---

## What You'll See

```
🎾 Tennis Manager 25 - Localization Translation

⠋ MP_UI ████████░░░░░░░░░░░░░░ 5/10 [00:30<01:15]

✅ Translation completed successfully!
```

---

## If Something Goes Wrong

| Error | What to Do |
|-------|-----------|
| "Virtual environment not found" | Ask IT to run setup (one-time) |
| "API error - retrying" | Wait, it will succeed (automatic retry) |
| "Sheet not found" | Check `config.py` sheet names match Excel |
| "No progress" | Check `logs/mt_run_*.log` for details |

---

## Configuration (Edit config.py)

```python
# Which sheets to translate
SHEETS_TO_TRAD = ["MP_UI", "Tuto"]

# Batch size: 20 (safe), 40 (recommended), 50 (fast)
BATCH_SIZE = 40

# Target language
TARGET_LANG = "Portuguese_BR"
```

---

## Results

- **Translations:** `localization.xlsx` → Portuguese_BR column
- **Log file:** `logs/mt_run_YYYYMMDD_HHMMSS.log`
- **Status:** `logs/mt_keys_YYYYMMDD_HHMMSS.csv`

---

## Typical Timing

| Size | Time |
|------|------|
| 100 segments | 1-2 min |
| 200 segments | 2-3 min |
| 500 segments | 5-7 min |

---

## For Detailed Help

→ See **README.md** (Troubleshooting section)

---

**Status:** Ready to use ✅
