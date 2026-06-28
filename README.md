# ApiDemos Android Automation Framework

An end-to-end Android UI + API test automation framework built with **Appium**, **TestNG**, and **Java 17**, targeting the [ApiDemos](https://github.com/appium/android-apidemos) reference app. Supports both local emulator runs and cloud execution via **SauceLabs**.

---

## Tech Stack

| Tool | Version |
|---|---|
| Java | 17 |
| Appium | 3.5.2 |
| Appium Java Client | 9.5.0 |
| Selenium | 4.32.0 |
| TestNG | 7.10.2 |
| ExtentReports | 5.1.2 |
| REST Assured | 5.4.0 |
| Maven | 3.x |
| Driver | UiAutomator2 |

---

## Prerequisites

- **JDK 17** installed and `JAVA_HOME` set
- **Android Studio / SDK** with an emulator named `emulator-5554` (API 30+)
- **Appium Server** 3.x installed (`npm install -g appium`) with UiAutomator2 driver (`appium driver install uiautomator2`)
- **ApiDemos APK** placed at `C:\Users\<you>\ApiDemos-debug.apk` (auto-installed on first run; app is used if APK not found)
- **Maven** 3.x on PATH
- **Eclipse IDE** (or IntelliJ) with TestNG plugin

---

## Project Structure

```
appium-tests/
├── src/test/java/
│   ├── base/
│   │   └── BaseTest.java           # Driver setup — local & SauceLabs
│   ├── pages/
│   │   ├── MainScreenPage.java     # Home screen interactions
│   │   └── TextScreenPage.java     # Text/EditText screen interactions
│   ├── tests/
│   │   ├── CombinedApiDemosTests.java       # Core UI tests (priority 1–14)
│   │   ├── EnterpriseTestSuite.java         # Enterprise category tests
│   │   ├── InstallLifecycleTests.java       # APK install / uninstall lifecycle
│   │   ├── InterruptSimulationTests.java    # Phone call / interrupt simulation
│   │   ├── MissingCoverageTests.java        # Deep link, notification, network tests
│   │   └── ApiIntegratedTests.java          # REST API + UI integration tests
│   ├── listeners/
│   │   └── ScreenshotListener.java  # Auto-screenshot on test failure
│   └── utils/
│       └── ExtentReportManager.java # HTML report singleton
├── src/test/resources/
├── testng.xml                       # Local test suite
├── testng-saucelabs.xml             # SauceLabs cloud suite
└── pom.xml
```

---

## Test Suites

| Suite | Class | Tests |
|---|---|---|
| Install Lifecycle | `InstallLifecycleTests` | APK install, launch, uninstall |
| Combined Core + Advanced | `CombinedApiDemosTests` | UI navigation, dialogs, preferences, text (14 tests) |
| Enterprise Categories | `EnterpriseTestSuite` | Graphics, media, OS features |
| Interrupt Simulation | `InterruptSimulationTests` | Incoming call simulation |
| Missing Coverage | `MissingCoverageTests` | Deep link, notification, network throttle |
| API Integration | `ApiIntegratedTests` | REST Assured + UI combined tests |

**Expected results:** 55 pass · 0 fail · 3 skip (graceful skips for context-dependent scenarios)

---

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/<your-username>/appium-tests.git
cd appium-tests
```

### 2. Install dependencies

```bash
mvn clean install -DskipTests
```

### 3. Place the APK

Copy `ApiDemos-debug.apk` to:

```
C:\Users\<your-username>\ApiDemos-debug.apk
```

> If the APK is not found, the framework will launch the already-installed app on the emulator.

---

## Running Tests

### Local (Emulator)

**Step 1 — Start emulator**

```bash
emulator -avd <your_avd_name>
```

**Step 2 — Start Appium server**

```bash
appium --port 4723
```

**Step 3 — Run tests**

```bash
mvn test -DsuiteXmlFile=testng.xml
```

Or right-click `testng.xml` in Eclipse → **Run As → TestNG Suite**.

---

### SauceLabs (Cloud)

**Step 1 — Upload APK to SauceLabs App Storage**

Log in to [app.saucelabs.com](https://app.saucelabs.com) and upload `ApiDemos-debug.apk` via **App Management**.

**Step 2 — Fill in credentials**

Edit `testng-saucelabs.xml`:

```xml
<parameter name="saucelabsUser" value="YOUR_SAUCE_USERNAME"/>
<parameter name="saucelabsKey"  value="YOUR_SAUCE_ACCESS_KEY"/>
```

Or export environment variables:

```bash
export SAUCE_USERNAME=your_username
export SAUCE_ACCESS_KEY=your_access_key
```

**Step 3 — Run**

```bash
mvn test -DsuiteXmlFile=testng-saucelabs.xml
```

---

## Reports & Screenshots

After a run:

| Artifact | Location |
|---|---|
| HTML Report | `test-output/ExtentReport.html` |
| Failure Screenshots | `test-output/screenshots/<testMethodName>.png` |

Open `ExtentReport.html` in any browser for a full pass/fail/skip breakdown with logs.

---

## Configuration Reference

### `testng.xml` parameters

| Parameter | Default | Description |
|---|---|---|
| `runMode` | `local` | `local` or `saucelabs` |
| `deviceName` | `emulator-5554` | ADB device name |
| `platformVersion` | `17` | Android API level |
| `saucelabsUser` | *(empty)* | SauceLabs username |
| `saucelabsKey` | *(empty)* | SauceLabs access key |

---

## Notes

- The `@BeforeClass` driver setup is inherited from `BaseTest`. All test classes extend `BaseTest` — do **not** add a separate `@BeforeClass` in subclasses.
- Tests 13 and 14 (`testTextInputFieldAcceptsAndRetainsValue`, `testKeyboardShowsAndHidesOnTextField`) will gracefully skip if no EditText sub-screen is found under the "Text" menu — this is expected and not a failure.
- `InstallLifecycleTests` uses `adb` shell commands and should not be run against SauceLabs (excluded from `testng-saucelabs.xml`).

---

## License

MIT
