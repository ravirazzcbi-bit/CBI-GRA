# Device Provisioning Guide (SETUP.md)

This guide explains how to provision devices for CSPs and Branch Heads using the CBI-GRA application.

---

## Overview

Each device is provisioned with:
- A **CSP-specific database** containing their assigned borrower accounts
- A **PIN** for authentication
- A **role** (CSP or ADMIN)

The provisioning process uses the `admin_import.py` script (Python backend tool) to generate per-CSP databases from Excel files.

---

## Prerequisites

### For Branch Admin (Device Preparation)
- Python 3.8+
- `admin_import.py` script (provided by development team)
- Excel file with borrower data
- Access to build Android APK

### For CSPs (Device Requirements)
- Android device (API 21+)
- ~500MB free storage
- Camera and GPS enabled
- Reliable wifi for initial app download

---

## Provisioning Flow

```
Excel Data
    ↓
admin_import.py (split)
    ↓
CSP-specific .db files
    ↓
Bundle in app assets/ or push to device
    ↓
Install APK
    ↓
First login: Set PIN
    ↓
Ready for recovery visits
```

---

## Step-by-Step Provisioning

### Phase 1: Prepare Data (Branch Admin)

**1.1 Prepare Excel File**

Create an Excel file with the following columns:
```
Account No | Name | Father Name | Address | Village | CSP Name | Sanction Amount | Outstanding | OTS Amt | Date of Sanction | NPA Date | Mobile
```

**Example:**
```
ACC-001 | Rajesh Kumar | Hari Kumar | House #12 | Gauripur | Rakesh | 50000 | 15000 | 5000 | 2022-01-15 | 2023-06-01 | 9876543210
ACC-002 | Priya Singh | Mohan Singh | House #45 | Kashipur | Rakesh | 75000 | 22000 | 8000 | 2022-03-20 | 2023-08-15 | 9876543211
```

**1.2 Run admin_import.py (Split Mode)**

```bash
python admin_import.py split \
  --excel borrowers.xlsx \
  --output ./databases/

# Output:
# databases/CBI-GRA_Rakesh.db
# databases/CBI-GRA_Priya.db
# ... (one file per CSP)
```

### Phase 2: Build & Install App (Branch Admin)

**2.1 For CSP Device (Single CSP)**

```bash
# Copy CSP's database to app assets
cp databases/CBI-GRA_Rakesh.db app/src/main/assets/csp_database.db

# Build APK
./gradlew assembleRelease

# Sign APK (if using keystore)
jarsigner -verbose -sigalg MD5withRSA -digestalg SHA1 \
  -keystore my-release-key.keystore \
  app/build/outputs/apk/release/app-release-unsigned.apk \
  alias_name

# Align APK
zipalign -v 4 app-release-unsigned.apk CBI-GRA-Rakesh.apk

# Install on device
adb install CBI-GRA-Rakesh.apk
```

**2.2 For Branch Head Device (All CSPs)**

```bash
# Run admin_import.py in build-admin-db mode
python admin_import.py build-admin-db \
  --databases ./databases/ \
  --output combined_database.db

# Copy combined database to app assets
cp combined_database.db app/src/main/assets/csp_database.db

# Build APK for admin role
# (Modify csp_users table to set role = 'ADMIN')

./gradlew assembleRelease

# Install on device
adb install CBI-GRA-Admin.apk
```

### Phase 3: First Login (CSP)

**3.1 Open App**
- App opens to PIN entry screen
- Shows: "Welcome, Rakesh"

**3.2 Set PIN (First Time Only)**
- Enter a 4-6 digit PIN (e.g., 1234)
- Confirm PIN
- App stores PIN hash locally (never transmitted)

**3.3 Search Screen**
- All assigned borrowers appear
- Can search by account number, name, mobile, village
- Ready to start recovery visits

---

## Advanced Provisioning Scenarios

### Scenario 1: Re-provision Device (New Year / Reassigned Accounts)

**Option A: Build Fresh APK**
```bash
# Update Excel with new borrowers for the CSP
python admin_import.py split --excel updated_borrowers.xlsx --output ./databases/

# Copy new database
cp databases/CBI-GRA_Rakesh.db app/src/main/assets/csp_database.db

# Build and install new APK
./gradlew assembleRelease
adb install CBI-GRA-Rakesh.apk
```

**Option B: Push Database at Runtime**
```bash
# Push updated .db file to device via USB/WhatsApp
adb push updated_database.db /sdcard/Download/

# CSP opens app → Sync screen → Select new database
# (Requires CspDatabaseHelper.reprovision() implementation)
```

### Scenario 2: Multiple CSPs on Branch Head's Device

```bash
# Branch Head has data from 6 CSPs combined
python admin_import.py build-admin-db \
  --databases ./databases/ \
  --output branch_admin_combined.db

# Dashboard shows metrics across all CSPs
# CSV/PDF exports include all CSP data
```

### Scenario 3: Mid-Year Account Update (Single CSP)

```bash
# Specific CSP gets additional accounts
python admin_import.py split \
  --excel incremental_accounts.xlsx \
  --output ./incremental_dbs/

# Merge with existing database
# OR provision fresh device with updated complete list
```

---

## Database File Details

### Location
- **On Device**: `/data/data/com.cbi.gra/databases/csp_database.db`
- **In App Assets**: `app/src/main/assets/csp_database.db`
- **External Export**: `Android/data/com.cbi.gra/files/sync_export/`

### Size
- Typical CSP database: 200KB (500-1000 borrowers)
- Grows with recovery visits: ~1-2MB per 1000 visits

### Backup & Recovery
```bash
# Backup device database via adb
adb pull /data/data/com.cbi.gra/databases/csp_database.db ./backup/

# Restore from backup
adb push ./backup/csp_database.db /data/data/com.cbi.gra/databases/
```

---

## PIN Management

### Setting PIN (First Login)
- User enters 4-6 digits
- App generates device salt (from Android ID)
- Hash = SHA-256(salt + PIN)
- Hash stored in `csp_users.pin_hash`
- Never transmitted anywhere

### PIN Reset
Currently **not supported in app**. To reset:

```bash
# Option 1: Uninstall and re-install with new PIN
adb uninstall com.cbi.gra
adb install CBI-GRA.apk

# Option 2: Clear app data (clears all local data including visits)
adb shell pm clear com.cbi.gra

# Option 3: Use database reprovision to reset
python admin_import.py reprovision \
  --device /sdcard/new_database.db
```

---

## Deployment Checklist

- [ ] Excel file prepared with borrower data
- [ ] admin_import.py script available
- [ ] Android SDK & gradle configured
- [ ] APK built and signed
- [ ] Device connected via USB (or adb over network)
- [ ] Device has storage space
- [ ] Camera & GPS permissions enabled
- [ ] First login completed with PIN set
- [ ] Borrower search works
- [ ] GPS capture tested
- [ ] Sync/export tested

---

## Troubleshooting

### Issue: App crashes on launch
**Solution:**
- Check that `csp_database.db` is in `assets/` folder
- Verify database schema matches SCHEMA_SQL in CspDatabaseHelper.kt
- Check logcat: `adb logcat | grep com.cbi.gra`

### Issue: "Account not found" during search
**Solution:**
- Verify CSP name in database matches login
- Check Excel data was imported correctly
- Query database: `adb shell sqlite3 /data/data/com.cbi.gra/databases/csp_database.db "SELECT COUNT(*) FROM borrowers;"`

### Issue: GPS always returns null
**Solution:**
- Enable GPS in emulator settings or on device
- Grant FINE_LOCATION permission at runtime
- Test with LocationHelper directly
- Ensure device has GPS hardware

### Issue: Photos not saving
**Solution:**
- Check external storage permissions
- Verify directory exists: `adb shell ls -la /sdcard/Android/data/com.cbi.gra/files/`
- Test with smaller image size
- Check available disk space: `adb shell df -h`

### Issue: PDF export fails
**Solution:**
- Check write permission to external files
- Verify file path is valid
- Ensure enough free space for PDF
- Check logcat for PdfDocument errors

---

## Admin Import Python Script (admin_import.py)

### Usage
```bash
# Split borrowers into per-CSP databases
python admin_import.py split \
  --excel borrowers.xlsx \
  --output ./output_dir/ \
  [--pin-default 1234]

# Build combined database for branch admin
python admin_import.py build-admin-db \
  --databases ./databases/ \
  --output combined.db

# Merge CSP exports back to branch database (Phase 4)
python admin_import.py merge \
  --branch-db branch.db \
  --exports ./exports/ \
  --output merged.db
```

### Expected Output
```
✓ Parsed borrowers.xlsx (1200 rows)
✓ Split into 6 CSP databases:
  - CBI-GRA_Rakesh.db (234 borrowers)
  - CBI-GRA_Priya.db (189 borrowers)
  - CBI-GRA_Anuj.db (245 borrowers)
  ...
✓ Generated PIN hashes with default salt
✓ Databases saved to ./output_dir/
```

---

## Security Notes

- **Database File**: Unencrypted SQLite (device-level Android security assumed)
- **PIN Storage**: Hashed with device salt (non-portable)
- **Backups**: Ensure backups are stored securely
- **Distribution**: Deliver APK via secure channel (not public app store)
- **Device Loss**: PIN provides minimal protection; physical security assumed

---

## Support

For provisioning issues or questions:
- Contact branch IT team
- Check app logs: `adb logcat -v long`
- Verify admin_import.py output for data issues
- Test on emulator before deploying to devices

---

**Last Updated**: August 2024  
**Version**: 1.0  
**Status**: Production Ready
