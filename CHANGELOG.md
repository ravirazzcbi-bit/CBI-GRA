# Changelog

All notable changes to CBI-GRA will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/), and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

---

## [1.0.0] - 2024-08-08

### Initial Release - Production Ready

#### Added
- **Core Features**
  - Borrower search and management
  - Recovery visit recording
  - Offline-first data persistence
  - QR code scanning for quick account lookup
  - GPS location capture with accuracy metrics

- **Data Capture**
  - Multi-photo support (borrower, house, ID)
  - Digital signature capture for borrower and CSP
  - Mobile number updates during visits
  - Recovery status categorization (6 statuses)
  - Collection amount and remarks recording

- **Security**
  - PIN-protected login (4-6 digits)
  - SHA-256 PIN hashing with device salt
  - Role-based access (CSP vs. ADMIN)
  - Per-device database provisioning

- **Dashboard & Reports** (Admin only)
  - Branch-wide summary metrics
  - CSP-wise performance tracking
  - Village-wise analysis
  - Recovery status breakdown
  - CSV and PDF export functionality

- **Data Management**
  - Offline database with SQLite
  - Visit history tracking per borrower
  - Automatic receipt generation (PDF)
  - Data export via WhatsApp, Bluetooth, email

- **User Interface**
  - Material Design 3 UI
  - Dark mode support
  - Responsive layouts for various screen sizes
  - Intuitive navigation

- **Development**
  - Kotlin implementation
  - Android Architecture Components
  - Modular layer structure (data/ui/util)
  - Comprehensive documentation

#### Technical Details
- **Min SDK**: Android 21
- **Target SDK**: Android 34
- **Database Schema**: 3 tables (borrowers, recovery_visits, csp_users)
- **Build Tool**: Gradle 8.5.0
- **Dependencies**: Material Design 3, AndroidX, Google Play Services, ZXing

---

## Planned Features (Roadmap)

### [1.1.0] - Q3 2024
- [ ] Biometric authentication option
- [ ] Offline map view for villages
- [ ] Batch status updates
- [ ] Performance optimizations
- [ ] Enhanced error logging

### [1.2.0] - Q4 2024
- [ ] Multi-language support
- [ ] Advanced filters and sorting
- [ ] Customizable report templates
- [ ] Audit trail for all operations
- [ ] Device-to-device sync over Bluetooth

### [2.0.0] - 2025
- [ ] (Under consideration) Server-based backup option
- [ ] (Under consideration) Real-time sync with branch server
- [ ] (Under consideration) Mobile app statistics dashboard
- [ ] (Under consideration) Integration with CBI's core banking system

---

## Version History

### [1.0.0] - 2024-08-08
**Initial Release**
- All core features implemented
- Device provisioning via admin_import.py
- Complete offline functionality
- Production-ready code

---

## Breaking Changes

None in version 1.0.0 (initial release).

### Future Breaking Changes (if any)
- Changes to database schema will require device re-provisioning
- PIN length validation changes would require PIN reset
- Role-based access changes might require app update

---

## Migration Guides

### Upgrading from 0.x to 1.0.0
This is the first production release. No migration needed.

### Future Updates
Migration guides will be provided here when schema changes occur.

---

## Known Issues

### v1.0.0
- None known at release. Please report any issues via GitHub Issues.

---

## Bug Fixes

### Recent Fixes
- None yet (initial release)

---

## Performance Improvements

### v1.0.0
- Database queries optimized with LIMIT clauses
- Photo compression for efficient storage
- Minimal memory footprint for offline use

---

## Deprecations

- None in version 1.0.0

---

## Security Updates

- None yet (initial release with secure defaults)

---

## Contributors

### Version 1.0.0 Contributors
- Development Team (Central Bank of India - Gauripur Branch)
- QA Team
- Branch Management

---

## Release Notes Summary

**CBI-GRA 1.0.0** marks the first production release of the Gauripur Recovery App. The app is fully functional for offline recovery visit tracking, with comprehensive data capture (photos, GPS, signatures) and reporting capabilities. All devices are provisioned via the `admin_import.py` script using Excel data provided by the branch.

### Key Highlights
✅ Completely offline operation (no internet required)  
✅ Secure device-specific PIN authentication  
✅ Multi-photo and signature capture  
✅ GPS-enabled visit tracking  
✅ Branch-wide dashboard for admins  
✅ CSV/PDF export for reporting  
✅ Zero server dependencies  

### Deployment
- Devices are provisioned with CSP-specific databases
- Branch Head device contains all CSP data combined
- Data export/merge happens offline
- No cloud services required

### Testing Status
- ✓ Functional testing on emulator and devices
- ✓ Offline functionality verified
- ✓ GPS and camera capture tested
- ✓ PDF generation and export tested
- ✓ Database integrity verified

---

## How to Report Issues

Found a bug or have a feature request?

1. Check [GitHub Issues](https://github.com/ravirazzcbi-bit/CBI-GRA/issues)
2. If not reported, create a new issue with:
   - Clear description
   - Steps to reproduce
   - Expected vs. actual behavior
   - Device info and Android version
   - Screenshots/logs if applicable

---

## Versioning Policy

We follow **Semantic Versioning (MAJOR.MINOR.PATCH)**:

- **MAJOR** (X.0.0): Breaking changes or major feature releases
- **MINOR** (0.X.0): New features added (backward compatible)
- **PATCH** (0.0.X): Bug fixes and small improvements

---

**Last Updated**: August 8, 2024  
**Maintained By**: Central Bank of India - Gauripur Branch
