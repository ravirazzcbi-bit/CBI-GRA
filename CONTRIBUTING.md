# Contributing to CBI-GRA

Thank you for your interest in contributing to the CBI Gauripur Recovery App! This guide outlines how to contribute code, report issues, and improve the project.

## Code of Conduct

- Be respectful and inclusive
- Focus on constructive feedback
- Help maintain a positive development environment

---

## Getting Started

### Prerequisites
- Android Studio 2023.1+
- Kotlin 1.9.24+
- Android SDK 34
- Git

### Setup Development Environment

```bash
# Clone the repository
git clone https://github.com/ravirazzcbi-bit/CBI-GRA.git
cd CBI-GRA

# Create a feature branch
git checkout -b feature/your-feature-name

# Build the project
./gradlew build

# Run tests
./gradlew test

# Install on connected device
./gradlew installDebug
```

---

## Development Workflow

### 1. Create a Feature Branch
```bash
git checkout -b feature/descriptive-name
```

Branch naming conventions:
- `feature/` – New features or enhancements
- `bugfix/` – Bug fixes
- `docs/` – Documentation updates
- `refactor/` – Code refactoring without behavior change
- `test/` – Test additions

### 2. Make Your Changes
- Follow Kotlin style guide ([Kotlin Coding Conventions](https://kotlinlang.org/docs/coding-conventions.html))
- Keep commits atomic and meaningful
- Write clear commit messages

**Commit Message Format:**
```
[TYPE] Brief description (50 chars max)

Detailed explanation (if needed). Explain the problem being solved
and how your solution addresses it.

Fixes #issue-number (if applicable)
```

Examples:
```
[FEATURE] Add offline sync validation
[BUGFIX] Fix GPS accuracy rounding error
[DOCS] Update device provisioning guide
[REFACTOR] Simplify RecoveryRepository queries
```

### 3. Test Your Changes
```bash
# Run all tests
./gradlew test

# Run specific test class
./gradlew test --tests MyTestClass

# Run with coverage
./gradlew test jacocoTestReport
```

### 4. Code Style & Linting
```bash
# Run Kotlin linter (if configured)
./gradlew lintDebug

# Format code
./gradlew ktlintFormat
```

### 5. Submit a Pull Request

1. Push your branch to GitHub:
   ```bash
   git push origin feature/your-feature-name
   ```

2. Open a Pull Request with:
   - Clear title and description
   - Link to related issues
   - Screenshots/GIFs for UI changes
   - Test results
   - Any migration steps if database schema changes

**PR Template:**
```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
- [ ] Documentation update

## Testing
- [ ] Tested on emulator
- [ ] Tested on physical device
- [ ] All unit tests pass

## Screenshots (if UI change)
[Add screenshots here]

## Checklist
- [ ] Code follows style guidelines
- [ ] No new warnings generated
- [ ] Tests added/updated
- [ ] Documentation updated
```

---

## Code Organization

### Layers
- **data/** – Database, repositories, preferences
- **ui/** – Activities, adapters, layouts
- **util/** – Helpers and utilities

### Best Practices
- Keep activities focused on UI logic
- Move business logic to repositories
- Use data classes for models
- Avoid direct database calls in UI code
- Handle permissions gracefully

### Example: Adding a New Feature

**1. Define Data Model** (`data/db/`)
```kotlin
data class MyEntity(
    val id: Long = 0,
    val name: String,
    val timestamp: String
)
```

**2. Add Database Operations** (`data/db/CspDatabaseHelper.kt`)
```sql
CREATE TABLE IF NOT EXISTS my_table (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    timestamp TEXT NOT NULL
);
```

**3. Add Repository Methods** (`data/repository/RecoveryRepository.kt`)
```kotlin
fun saveMyEntity(entity: MyEntity): Long {
    val db = dbHelper.openForWrite()
    val values = ContentValues().apply {
        put("name", entity.name)
        put("timestamp", entity.timestamp)
    }
    return db.insert("my_table", null, values)
}
```

**4. Update UI** (`ui/myfeature/MyActivity.kt`)
```kotlin
class MyActivity : AppCompatActivity() {
    private lateinit var repository: RecoveryRepository
    
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        repository = RecoveryRepository(this)
        // Use repository.saveMyEntity(entity)
    }
}
```

**5. Write Tests** (`test/...`)
```kotlin
@Test
fun testSaveMyEntity() {
    val entity = MyEntity(name = "Test")
    val id = repository.saveMyEntity(entity)
    assertTrue(id > 0)
}
```

---

## Database Schema Changes

**If you need to modify the database schema:**

1. Update `SCHEMA_SQL` in `CspDatabaseHelper.kt`
2. Increment `DB_VERSION`
3. Implement migration logic in `onUpgrade()`
4. Document the change with a comment
5. Update this README's database schema section

Example:
```kotlin
override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
    if (oldVersion < 2) {
        // Add new column to borrowers table
        db.execSQL("ALTER TABLE borrowers ADD COLUMN new_field TEXT")
    }
}
```

---

## Reporting Issues

### Bug Report Template
```markdown
**Description**
Clear description of the bug

**Steps to Reproduce**
1. Open app
2. Navigate to [screen]
3. Click [button]
4. Observe [unexpected behavior]

**Expected Behavior**
What should happen

**Actual Behavior**
What actually happens

**Environment**
- Android version: [e.g., 11]
- Device: [e.g., Samsung A51]
- App version: [e.g., 1.0]

**Screenshots/Logs**
[Attach if possible]
```

### Feature Request Template
```markdown
**Description**
Clear description of the requested feature

**Use Case**
Why is this feature needed?

**Proposed Solution**
How should this feature work?

**Alternatives Considered**
Other approaches considered
```

---

## Documentation

### Updating Docs
- Keep README.md in sync with code changes
- Add comments for complex logic
- Document new permissions required
- Update CHANGELOG.md for releases

### Doc Structure
```
docs/
├── SETUP.md          – Device provisioning guide
├── ARCHITECTURE.md   – Detailed architecture
├── API.md            – API reference (if applicable)
└── FAQ.md            – Frequently asked questions
```

---

## Performance Guidelines

- Limit database queries in loops
- Use LIMIT clauses on searches (max 100 results)
- Compress photos before saving
- Cache borrower lists in memory for searches
- Avoid blocking UI thread

### Profile Your Changes
```bash
# Profile with Android Profiler in Android Studio
# Monitor: CPU, Memory, Network, Energy
```

---

## Security Considerations

- Never log sensitive data (PINs, locations, personal info)
- Use SharedPreferences for non-sensitive data only
- Validate all user inputs
- Use secure file permissions
- Hash PINs with salt
- Avoid hardcoding credentials/secrets

---

## Release Process

### Versioning
We follow [Semantic Versioning](https://semver.org/):
- MAJOR.MINOR.PATCH (e.g., 1.0.0)
- MAJOR – Breaking changes
- MINOR – New features (backward compatible)
- PATCH – Bug fixes

### Release Checklist
- [ ] Update version in `build.gradle.kts`
- [ ] Update CHANGELOG.md
- [ ] All tests passing
- [ ] Code review completed
- [ ] Tag release: `git tag v1.0.0`
- [ ] Push tag: `git push origin v1.0.0`
- [ ] Build release APK: `./gradlew assembleRelease`

---

## Getting Help

- **Documentation**: Check README.md and docs/ folder
- **Issues**: Search existing issues on GitHub
- **Discussions**: Use GitHub Discussions for questions
- **Email**: Contact branch IT team for deployment questions

---

## License

By contributing to CBI-GRA, you agree that your contributions will be licensed under the same license as the project (internal use for Central Bank of India - Gauripur Branch).

---

Thank you for contributing! 🙏
