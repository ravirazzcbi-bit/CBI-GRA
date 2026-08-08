# CBI Gauripur Recovery App (CBI-GRA)

## Overview
CBI-GRA is an Android application designed for the **Central Bank of India - Gauripur Branch** to streamline loan recovery operations. The app enables Collection Service Personnel (CSPs) and Branch Heads to manage recovery visits, capture borrower data, and generate comprehensive reports—all **completely offline** with no server dependency.

## Key Features

### 📱 Core Functionality
- **Borrower Management**: Search and manage borrower accounts by account number, name, mobile, or village
- **Recovery Visits**: Log recovery visits with GPS location, photo captures, and signatures
- **Offline-First**: Complete functionality without internet connection
- **QR Code Scanning**: Quick account lookup via QR codes on borrower files
- **Data Export**: Export recovery data to CSV/PDF for branch reporting

### 🔐 Security & Access
- **PIN-Protected Login**: Device-specific 4-6 digit PIN for authentication
- **Role-Based Access**: Separate interfaces for CSPs and Branch Heads (Admins)
- **Per-Device Database**: Each device provisioned with CSP-specific borrower data
- **No Cloud Transmission**: All data remains on-device until user explicitly exports

### 📊 Dashboard & Reports
- **Branch-Wide Summaries**: Total accounts, outstanding amounts, collections (for Admins)
- **CSP-wise Performance**: Individual CSP metrics and progress tracking
- **Village-wise Analysis**: Recovery status by geographic area
- **Status Tracking**: Categorized recovery outcomes (Paid, Partial, Refused, etc.)
- **PDF/CSV Export**: Generate shareable recovery reports

### 📸 Data Capture
- **GPS Coordinates**: Accurate visit location with accuracy metrics
- **Multi-Photo Support**: Borrower photo, house photo, and ID photo capture
- **Digital Signatures**: Capture and embed borrower and CSP signatures in receipts
- **Mobile Number Updates**: Collect updated contact information during visits

### 🎯 Recovery Status Categories
- ✅ Paid in Full
- 📊 Partial Payment
- ❌ Refused to Pay
- ⏰ Promise to Pay (PTP)
- 🔍 Borrower Not Found
- 🏠 House Locked

---

## Architecture

### Technology Stack
- **Language**: Kotlin
- **UI Framework**: Android Material3 / Material Design
- **Database**: SQLite (on-device)
- **Build System**: Gradle 8.5.0
- **Min SDK**: Android 21+
- **Target SDK**: Android 34+

### Project Structure

```
com.cbi.gra/
├── data/
│   ├── db/              # Database models & helpers
│   │   ├── Borrower.kt
│   │   ├── RecoveryVisit.kt
│   │   ├── RecoveryStatus.kt (enum)
│   │   └── CspDatabaseHelper.kt
│   ├── prefs/           # Preference management
│   │   └── SessionManager.kt
│   └── repository/      # Data access layer
│       └── RecoveryRepository.kt
├── ui/
│   ├── login/           # Authentication
│   │   └── LoginActivity.kt
│   ├── search/          # Borrower search & home
│   │   ├── SearchActivity.kt
│   │   └── BorrowerAdapter.kt
│   ├── detail/          # Recovery visit detail form
│   │   └── BorrowerDetailActivity.kt
│   ├── dashboard/       # Admin dashboard & reports
│   │   └── DashboardActivity.kt
│   └── sync/            # Data export/sync
│       └── SyncActivity.kt
├── util/
│   ├── LocationHelper.kt       # GPS capture
│   ├── PhotoCaptureHelper.kt   # Camera integration
│   ├── SignatureView.kt        # Custom signature pad
│   ├── ReceiptGenerator.kt     # PDF receipt generation
│   └── PinHasher.kt            # PIN hashing (SHA-256)
└── CbiGraApp.kt         # Application initialization
```

### Database Schema

#### borrowers
Stores borrower account information (read-only, provisioned per device):
```sql
CREATE TABLE borrowers (
    account_no TEXT PRIMARY KEY,
    cif_no TEXT,
    name_of_borrower TEXT NOT NULL,
    father_name TEXT,
    address TEXT,
    village TEXT,
    csp_name TEXT NOT NULL,
    sanction_amount REAL,
    total_outstanding REAL,
    ots_amt REAL,
    date_of_sanction TEXT,
    npa_date TEXT,
    mobile_number TEXT
);
```

#### recovery_visits
Records each recovery visit with all captured data:
```sql
CREATE TABLE recovery_visits (
    visit_id INTEGER PRIMARY KEY AUTOINCREMENT,
    account_no TEXT NOT NULL,
    csp_name TEXT NOT NULL,
    visit_datetime TEXT NOT NULL,
    gps_lat REAL, gps_lng REAL, gps_accuracy_m REAL,
    borrower_photo_path TEXT,
    house_photo_path TEXT,
    id_photo_path TEXT,
    borrower_signature_path TEXT,
    csp_signature_path TEXT,
    recovery_status TEXT NOT NULL,
    collection_amount REAL DEFAULT 0,
    remarks TEXT,
    updated_mobile_number TEXT,
    synced INTEGER DEFAULT 0,
    device_id TEXT,
    created_at TEXT,
    UNIQUE(device_id, account_no, visit_datetime)
);
```

#### csp_users
Single-row table per device storing CSP identity and PIN:
```sql
CREATE TABLE csp_users (
    csp_id INTEGER PRIMARY KEY AUTOINCREMENT,
    csp_name TEXT UNIQUE NOT NULL,
    pin_hash TEXT,
    role TEXT DEFAULT 'CSP',
    active INTEGER DEFAULT 1,
    created_at TEXT
);
```

---

## User Flows

### 1. **CSP Login & Search**
1. Open app → PIN entry screen
2. First login: Set 4-6 digit PIN (stored as SHA-256 hash with device salt)
3. Subsequent logins: Verify PIN
4. Redirected to Search screen showing all assigned borrowers
5. Search by account number, name, mobile, or village
6. Scan QR code on borrower's file for instant lookup

### 2. **Recovery Visit Recording**
1. Tap a borrower to open detail form
2. Capture GPS location (requires FINE_LOCATION permission)
3. Take borrower, house, and ID photos
4. Select recovery status from dropdown
5. Enter collection amount (if any)
6. Add optional remarks
7. Capture borrower and CSP signatures
8. **Save Visit** → PDF receipt generated and ready to share
9. Visit saved offline with timestamp and device ID

### 3. **Dashboard (Admin Only)**
1. Available only to users with role = "ADMIN"
2. View branch-wide summary cards:
   - Total accounts, visited vs. pending
   - Total outstanding vs. collected
3. Analyze performance by CSP and village
4. View recovery status breakdown
5. Export branch reports as CSV or PDF

### 4. **Data Export/Sync**
1. Tap "Sync" button on Search screen
2. View count of visits recorded on device
3. Export database file (`.db`) for transmission
4. Share via WhatsApp, Bluetooth, email, or USB (no internet required)
5. Branch admin merges exports from multiple CSPs using `admin_import.py`

---

## Permissions Required

```xml
<uses-permission android:name="android.permission.CAMERA" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

All permissions are requested at runtime and can be denied—the app gracefully degrades (e.g., GPS capture shows status instead of failing).

---

## Device Provisioning

### Initial Setup
1. **Branch Admin** runs `admin_import.py` with:
   - Excel file of borrowers for each CSP
   - PIN for CSP
2. Output: `CBI-GRA_<CSP_NAME>.db` for each CSP
3. For CSPs: Rename to `csp_database.db`, bundle in app's `assets/` folder
4. For Branch Head: Run `admin_import.py build-admin-db` to merge all CSP data into one device

### Re-provisioning (New Year / Account Reassignment)
1. Build fresh app with updated `assets/csp_database.db`
2. **OR** use `CspDatabaseHelper.reprovision(context, newDbFile)` to push updated DB at runtime

---

## Dark Mode Support
- Toggle dark mode via switch on Search screen
- Preference persisted in SharedPreferences
- Respects system settings on app startup

---

## Dependencies
- **Material Design 3**: `com.google.android.material:material:1.x`
- **AndroidX**: Core, AppCompat, RecyclerView
- **Google Play Services**: Location API
- **Barcode Scanner**: ZXing library for QR code scanning
- **PDF Generation**: Built-in `android.graphics.pdf.PdfDocument`

---

## Building & Installation

### Prerequisites
- Android Studio (2023.1+)
- Kotlin 1.9.24+
- Android SDK 34

### Build Steps
```bash
# Clone repository
git clone https://github.com/ravirazzcbi-bit/CBI-GRA.git
cd CBI-GRA

# Build APK
./gradlew assembleDebug

# Install on device
./gradlew installDebug

# Build release APK
./gradlew assembleRelease
```

### Running Tests
```bash
./gradlew test
```

---

## Offline Data Persistence

All user-captured data persists locally in the SQLite database:
- Recovery visits (photos, signatures, GPS, amounts)
- Updated mobile numbers
- Borrower visit history
- Device sync status

**Important**: Data is considered synced only when explicitly exported and merged by branch admin. The `synced` flag in `recovery_visits` is not the source of truth—it's used for UI indication only.

---

## FAQ

### Q: Can the app work without internet?
**A:** Yes—the app is entirely offline. No internet connection is required for any operation (login, search, visit recording, report generation). Data export requires manual transfer (WhatsApp, Bluetooth, USB) but no cloud service.

### Q: How is PIN security handled?
**A:** PINs are hashed using SHA-256 with a per-device salt (derived from Android ID) and stored only in the local database. They are never transmitted anywhere. The threat model assumes physical phone loss/theft.

### Q: Can multiple CSPs use the same device?
**A:** No—each device is provisioned for exactly one CSP. The database is CSP-specific, and login confirms identity rather than switching users.

### Q: What happens if I uninstall the app?
**A:** The database is stored in the app's private directory and is deleted on uninstall. Re-provisioning the device requires a fresh app build with updated `assets/csp_database.db`.

### Q: How do I share recovery data?
**A:** Tap "Sync" → "Export & Share My Data". A database file is created and can be shared via any channel (WhatsApp, email, Bluetooth, USB drive). No internet required.

### Q: Where are photos and signatures stored?
**A:** Photos and signature PNGs are stored in `Android/data/com.cbi.gra/files/` subdirectories organized by account number and file type. These persist across app sessions and survive app updates.

### Q: What if GPS is unavailable?
**A:** GPS capture degrades gracefully—the app displays "Could not get location" and allows the visit to proceed without GPS data. Location is optional.

### Q: How do I update borrower data mid-year?
**A:** Use `CspDatabaseHelper.reprovision(context, newDbFile)` or rebuild the app with an updated `assets/csp_database.db` file. The new database replaces the existing one.

---

## Architecture Decisions

### Why SQLite?
- No server dependency
- Built-in Android support
- Reliable offline persistence
- Portable (database file can be exported/merged)

### Why Per-Device Provisioning?
- Reduces data on resource-constrained phones
- Security: Each CSP only sees their own accounts
- Simplifies sync: No complex conflict resolution needed

### Why PIN Instead of Biometric?
- Works in rural areas with older Android devices
- Simple for branch staff (4-6 digits is memorable)
- No dependency on hardware capabilities

### Why Client-Side PDF Generation?
- No server required
- Instant receipt generation
- Receipt embeds all captured data (GPS, signatures, photos)

---

## Performance Considerations

### Search Optimization
- Queries use indexed `account_no` and `csp_name` columns
- LIMIT 100 on search results to prevent UI lag
- Borrower list loads once on app start and filters in-memory on text input

### Database Size
- Typical CSP: 500-1000 borrowers = ~200KB database
- Photos/signatures: ~1-2MB per visit (compressed)
- No cleanup required—older visits remain for history

### Battery & Network
- GPS capture uses `Priority.PRIORITY_HIGH_ACCURACY` for accurate coordinates
- All I/O (photos, signatures) uses app-private storage to avoid system scans
- No background services—app is foreground-only

---

## License
Internal use for Central Bank of India - Gauripur Branch only.

---

## Support & Feedback
For issues, feature requests, or deployment questions, contact the branch IT team.

---

**Version**: 1.0  
**Last Updated**: August 2024  
**Status**: Production Ready
