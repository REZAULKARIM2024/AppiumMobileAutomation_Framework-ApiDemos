# 📱 Appium Mobile Automation Framework — ApiDemos

A Java-based mobile test automation framework built with **Appium**, **TestNG**, and the **Page Object Model (POM)**, targeting Google's `ApiDemos` sample Android application. Designed and built incrementally — from raw environment setup through senior-level automation patterns like test isolation, self-healing locators, and graceful degradation.

![Java](https://img.shields.io/badge/Java-17-orange)
![Appium](https://img.shields.io/badge/Appium-3.x-purple)
![Selenium](https://img.shields.io/badge/Selenium-4.32.0-green)
![TestNG](https://img.shields.io/badge/TestNG-7.10.x-blue)
![Maven](https://img.shields.io/badge/Build-Maven-red)
![Tests](https://img.shields.io/badge/Tests-58%20total%20%7C%200%20failures-brightgreen)

---

## 🛠 Tech Stack

| Layer | Tool |
|---|---|
| Language | Java 17 |
| Mobile Driver | Appium 3.x (UiAutomator2 Driver) |
| Web Driver Protocol | Selenium 4.32.0 |
| Test Runner | TestNG 7.10.x |
| Build Tool | Maven |
| Reporting | ExtentReports 5.1.2 (HTML) |
| API Testing | REST Assured 5.4.0 |
| Device | Android Emulator (Pixel-class, API 33–37) |

---

## 📁 Project Structure

```
appium-tests/
├── pom.xml
├── testng.xml                          # Master regression suite (run this!)
├── crossdevice-testng.xml              # Multi-device/parallel skeleton suite
└── src/main/java/
    ├── base/
    │   └── BaseTest.java                # Driver setup/teardown, Extent flush
    ├── pages/                           # Page Object Model
    │   ├── MainScreenPage.java
    │   ├── AccessibilityPage.java
    │   ├── AnimationPage.java
    │   ├── TextScreenPage.java
    │   └── PermissionPage.java
    ├── listeners/
    │   └── ScreenshotListener.java       # Auto screenshot on test failure
    ├── utils/
    │   ├── WaitUtils.java                 # Explicit wait + custom retry
    │   └── ExtentReportManager.java       # HTML report singleton
    └── tests/
        ├── CombinedApiDemosTests.java     # Core + advanced UI tests (POM, gestures, waits)
        ├── EnterpriseTestSuite.java       # Smoke, lifecycle, negative, security, a11y
        ├── InstallLifecycleTests.java     # Install / upgrade / uninstall flows
        ├── InterruptSimulationTests.java  # Call / SMS / network / battery interrupts
        ├── MissingCoverageTests.java      # Deep links, notifications, slow network/retry
        └── ApiIntegratedTests.java        # Backend API verification (REST Assured)
```

---

## ✅ Test Coverage

| Category | Implemented | Notes |
|---|---|---|
| Smoke | ✅ | App launch, basic navigation |
| Regression | ✅ | Menu counts, text validation, back-nav |
| App Lifecycle | ✅ | Kill/relaunch, background/foreground, rotation |
| Navigation | ✅ | Multi-step back button, rapid menu switching, deep links |
| Device Behavior | ✅ | Screen dimensions, platform version, orientation |
| Permissions | ✅ | Grant/deny flow handling (utility-level) |
| Negative Testing | ✅ | Invalid element interaction, empty input handling |
| Data-Driven | ✅ | `@DataProvider`-based multi-screen navigation |
| Performance | ✅ | Scroll response time, element wait thresholds, launch time |
| Security | ✅ | Post-termination state clearing |
| Accessibility | ✅ | Content-desc labels, tap target sizing |
| Install/Upgrade/Uninstall | ✅ | Fresh install → reinstall → uninstall → reinstall cycle |
| Interrupt Simulation | ✅ | Incoming call, SMS, network loss, low battery |
| Network Conditions | ✅ | Slow network (GSM throttling), drop + retry/recovery |
| Notifications | ✅ | App-generated notification tray check (graceful skip if unavailable) |
| API Integration | ✅ | REST Assured — status codes, field validation, negative 404 |
| Cross-Device/OS | ⚠️ Skeleton | Parameterized `BaseTest` ready for multi-emulator / Sauce Labs — see below |
| Auth / Cart / Checkout / Payment | 🚫 Not applicable | `ApiDemos` has no such flows — commented templates included in `EnterpriseTestSuite` for adapting to a real app |

**58 individual test executions** across 6 test classes, run from a single master TestNG suite (`testng.xml`), with HTML reporting and automatic failure screenshots.

### 📈 Latest Run

```
Total tests run: 58
Passed:  55
Failed:   0
Skipped:  3   (gracefully skipped — environment/APK-version dependent, not assertion failures)
```

The 3 skips relate to a single sub-screen (`Text → Editing`) whose exact item label varies slightly across `ApiDemos` APK builds. The framework detects this at runtime and skips with a clear reason rather than reporting a false failure — see [Known Limitations](#-known-limitations).

---

## 🚀 Getting Started

### Prerequisites
- Java 17 (`java -version`)
- Maven (bundled with most IDEs, or standalone)
- Node.js + Appium Server (`npm install -g appium && appium driver install uiautomator2`)
- Android SDK + an emulator (Android Studio → Device Manager)
- Eclipse / IntelliJ with TestNG plugin

### Run the full suite
```bash
# 1. Start Appium server
appium

# 2. Start your emulator and confirm it's visible
adb devices

# 3. Run the master suite
#    In Eclipse/IntelliJ: right-click testng.xml → Run As → TestNG Suite
```

> ⚠️ Always right-click the **`testng.xml`** file itself (not the project or a single test class) — this ensures the `deviceName` / `platformVersion` parameters defined in the XML are passed correctly to `BaseTest`.

### Run a single test class
Right-click the class → **Run As → TestNG Test** (only works for classes without required XML parameters, e.g. `InstallLifecycleTests`, `ApiIntegratedTests`).

---

## 📊 Reports

| Artifact | Location |
|---|---|
| Extent HTML report | `test-output/ExtentReport.html` |
| Failure screenshots | `test-output/screenshots/` |
| TestNG native report | `test-output/index.html` |

---

## 🌐 Cross-Device / Cross-OS Testing

`BaseTest` is parameterized (`deviceName`, `platformVersion`), so the same test classes can run against multiple devices:

- **Local:** spin up a second emulator at a different API level, add a second `<test>` block in `crossdevice-testng.xml` with `parallel="tests"`.
- **Cloud (Sauce Labs / BrowserStack):** swap the Appium server URL for the cloud's hub URL, upload the APK to cloud storage, and reference real device names from the platform's device list. A `SauceLabsBaseTest` variant is easy to add following the same pattern as `BaseTest`.

---

## 🎓 Key Engineering Practices Demonstrated

- **Page Object Model** for maintainable, reusable locators
- **Test isolation** via `ensureOnMainScreen()` — prevents one failing test from cascading into the next
- **State-based assertions** over brittle literal-text matching (navigation verified by screen-state change, not hardcoded text)
- **Self-healing element discovery** for UI elements whose exact labels vary across APK builds, with scroll-based fallback search
- **Soft vs. hard assertions** — multi-check validation without short-circuiting on first failure
- **Custom retry & explicit-wait utilities** — replacing flaky `Thread.sleep()` calls
- **Graceful skip handling** for environment-dependent scenarios (`SkipException` instead of false failures)
- **Maven `dependencyManagement` vs `dependencies`** used correctly to centralize version control
- **Screenshot-on-failure listener** + **Extent HTML reporting** for CI-friendly diagnostics

---

## 📌 Known Limitations

- `ApiDemos` is a demo app — it has no login, cart, checkout, or push-notification-badge flows. Templates for these are included (commented out) in `EnterpriseTestSuite.java`, ready to adapt for a real production app.
- The `Text → Editing` sub-screen's item label is not 100% consistent across all `ApiDemos` APK builds; tests targeting it use a scroll-based search with a graceful `SkipException` fallback rather than a hard failure when the exact label can't be confirmed.
- Interrupt simulation commands (`adb emu call/sms/power`) work only on the **Android Emulator**, not physical devices.
- Cross-device suite is provided as a ready-to-run skeleton; running it in parallel requires a second local emulator or a cloud device farm account.

---

## 🤝 Contributing / Adapting to Your Own App

This framework's architecture (`BaseTest` + `pages/` + `utils/` + `tests/`) is intentionally app-agnostic. To point it at a real application:

1. Replace `appPackage` / `appActivity` / APK path in `BaseTest.java`
2. Add new Page Object classes under `pages/` for your app's screens
3. Uncomment and implement the `auth_*` / `businessFlow_*` / `api_*` templates in `EnterpriseTestSuite.java`

---

## 📞 Contact & Support

**Author:** Rezaul Karim
**Email:** [rknyc2021@gmail.com](mailto:rknyc2021@gmail.com)
**Phone:** +1 347-221-3047
**LinkedIn:** [Rezaul Karim](https://linkedin.com/in/rezaul-karim-803a3b273)
**GitHub:** [@REZAULKARIM2024](https://github.com/REZAULKARIM2024)

---

## 📄 License

This project is intended as a learning/portfolio reference implementation. Feel free to fork and adapt.
