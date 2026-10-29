# Avoora Mobile — QA Automation Framework

WebdriverIO v9 + Appium 3 automation framework for the Avoora Health mobile application.
Supports Android (Phase 1 + Phase 2). iOS support pending Mac availability.
Built for team collaboration — no hardcoded credentials or device IDs.

---

## Current Status

| Module                                     | Test Cases Automated                     | Status      | Last Updated |
| ------------------------------------------ | ---------------------------------------- | ----------- | ------------ |
| Login                                      | 24                                       | ✅ Complete | 9 Jul 2026   |
| Forgot Password                            | Included in Login module                 | ✅ Complete | 9 Jul 2026   |
| Sign Up — Patient                          | 19                                       | ✅ Complete | 5 Aug 2026   |
| Sign Up — Guardian                         | 1 (happy path to OTP screen)             | ✅ Complete | 1 Sep 2026   |
| Sign Up — Carer                            | 1 (happy path to OTP screen)             | ✅ Complete | 6 Sep 2026   |
| Login — Carer (My Patients landing)        | 1                                        | ✅ Complete | 8 Sep 2026   |
| Carer Invitation Flow (Send/Accept/Reject) | 3                                        | ✅ Complete | 8 Sep 2026   |
| Home Page                                  | 7                                        | ✅ Complete | 7 Aug 2026   |
| Consultation                               | 21                                       | ✅ Complete | 7 Aug 2026   |
| Health Diary — Voice Note                  | 10                                       | ✅ Complete | 7 Aug 2026   |
| Health Diary — Written Note                | 8 (Phase 2)                              | ✅ Complete | 25 Aug 2026  |
| Documents                                  | 14                                       | ✅ Complete | 31 Aug 2026  |
| Profile                                    | Role verification + email/account tests  | ✅ Complete | 1 Sep 2026   |
| Calendar View                              | Not started                              | ⏳ Planned  | —            |
| Phase 2 — Priority Features                | 5 of 5 features complete (29 test cases) | ✅ Complete | 25 Aug 2026  |

---

## Prerequisites

Install the following on your machine before starting.

### Everyone

| Tool             | Version      | Install                                             |
| ---------------- | ------------ | --------------------------------------------------- |
| Node.js          | v18 or above | https://nodejs.org                                  |
| Java JDK         | 11 or above  | https://adoptium.net                                |
| Appium Inspector | Latest       | https://github.com/appium/appium-inspector/releases |

Check your versions:

```bash
node -v
java -version
```

### Android

| Tool           | Notes                                            |
| -------------- | ------------------------------------------------ |
| Android Studio | Install via https://developer.android.com/studio |
| ADB            | Comes with Android Studio. Add to PATH.          |

Add these to your Windows **Environment Variables → PATH**:

```
C:\Users\<YourName>\AppData\Local\Android\Sdk\platform-tools
C:\Users\<YourName>\AppData\Local\Android\Sdk\tools
C:\Users\<YourName>\AppData\Local\Android\Sdk\emulator
```

Also add a new **System Variable**:

```
ANDROID_HOME = C:\Users\<YourName>\AppData\Local\Android\Sdk
```

Verify ADB is working:

```bash
adb version
adb devices
```

### iOS (Mac required — currently blocked)

| Tool                     | Notes                           |
| ------------------------ | ------------------------------- |
| Xcode                    | Install from Mac App Store      |
| Xcode Command Line Tools | `xcode-select --install`        |
| ios-deploy               | `npm install -g ios-deploy`     |
| libimobiledevice         | `brew install libimobiledevice` |

> ⚠️ iOS automation requires macOS. This remains an open blocker.

---

## Setup (First Time)

### 1. Clone the repo

```bash
git clone https://github.com/iclickonline/avoora-test-automation.git
cd avoora-test-automation
```

### 2. Install dependencies

```bash
npm install
```

### 3. Install Appium and drivers

```bash
npm install -g appium
appium driver install uiautomator2   # Android
appium driver install xcuitest       # iOS (Mac only)
```

Verify:

```bash
appium -v
appium driver list
```

### 4. Set up your .env file

```bash
copy .env.example .env   # Windows
cp .env.example .env     # Mac/Linux
```

Open `.env` and fill in your values:

```env
TEST_EMAIL=your.test.email@example.com
TEST_PASSWORD=YourTestPassword123!

# Additional test accounts — see Test Accounts section below
TEST_EMAIL_FORGOT_PASSWORD=
TEST_EMAIL_NO_CONSULTATIONS=
TEST_PASSWORD_NO_CONSULTATIONS=
TEST_EMAIL_HOME=
TEST_EMAIL_CARER=
TEST_PASSWORD_CARER=
TEST_EMAIL_GUARDIAN=
TEST_PASSWORD_GUARDIAN=

ANDROID_UDID=<your device UDID from adb devices>
ANDROID_DEVICE_NAME=<your device name e.g. emulator-5554>

IOS_UDID=<your device UDID>
IOS_DEVICE_NAME=<your device name>
IOS_PLATFORM_VERSION=17.0

ANDROID_APP_ID=com.avoora.health.stage
IOS_BUNDLE_ID=com.avoora.health.stage

APPIUM_HOST=127.0.0.1
APPIUM_PORT=4723
```

> ⚠️ The `.env` file is in `.gitignore` and must NEVER be committed to the repo.

### 5. Install the Avoora app on your device

```bash
# Get the latest staging APK from the development team, then:
adb install path\to\app-stage-release.apk
```

> ⚠️ The APK is not stored in the repo. Request the latest staging build from the dev team.

---

## Every Time You Start Work

The emulator does not persist app installations between restarts. Each time you begin a session:

**Step 1** — Start the emulator in Android Studio (Device Manager → ▶ Play)

**Step 2** — Wait for it to fully boot, then install the app:

```bash
adb install -r path\to\app-stage-release.apk
```

**Step 3** — Start Appium in a dedicated terminal:

```bash
appium
```

**Step 4** — Open a second terminal and run your tests.

---

## Running Tests

### Android

```bash
# Run all tests
npx wdio run wdio.android.conf.ts

# Run a specific module
npx wdio run wdio.android.conf.ts --spec test/specs/login.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/signup.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/home.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/consultation.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/health_diary.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/documents.spec.ts

# Guardian / Carer sign-up and login flows
npx wdio run wdio.android.conf.ts --spec test/specs/guardian_signup.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/carer_signup.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/carer_login.spec.ts

# Carer invitation flow (send / accept / reject) — run as a full file,
# since MEMBERS-TC-002 depends on MEMBERS-TC-001 leaving a Pending invite
npx wdio run wdio.android.conf.ts --spec test/specs/carer_invitation.spec.ts

# Phase 2 priority features
npx wdio run wdio.android.conf.ts --spec test/specs/phase2_home.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/phase2_profile.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/phase2_carer.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/phase2_health_diary.spec.ts
npx wdio run wdio.android.conf.ts --spec test/specs/phase2_delete_account.spec.ts

# Run a single test case within any file
npx wdio run wdio.android.conf.ts --spec test/specs/<file>.spec.ts --mochaOpts.grep "TEST-ID"
```

> `wdio.conf.ts` is the shared base config only (timeouts, reporters, Mocha) — it has no `capabilities` block and cannot be run directly. Always run against `wdio.android.conf.ts` (or `wdio.ios.conf.ts` once available).

> `carer_invitation.spec.ts`'s three tests are order-dependent by design: TC-001 sends an invite and leaves it Pending, TC-002 accepts that same invite, and TC-003 resets/resends before testing reject. Don't run TC-002 alone via `--mochaOpts.grep` unless a Pending invite already exists.

### iOS

Blocked — pending Mac availability.

---

## Test Accounts

| Account                          | Purpose                                                                                                                    |
| -------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| `avooratest1@mailinator.com`     | Primary test account — Login, Home, Consultation, Health Diary, Profile, and the Patient side of the Carer invitation flow |
| `avooratest2@mailinator.com`     | Secondary account — Forgot Password tests only                                                                             |
| `avooratestcon@mailinator.com`   | Empty-state account — no appointment type/consultations ever recorded                                                      |
| `avooratestnew1@mailinator.com`  | Home module — appointment type starts unselected                                                                           |
| `avooratestcarer@mailinator.com` | Dedicated Carer account — Connected Patient Removal, Carer sign-up/role tests                                              |
| `isutest@mailinator.com`         | Verified Guardian account — used for Guardian role-on-Profile verification                                                 |
| `avoorac1@mailinator.com`        | Verified Carer account — used for Carer login (My Patients landing) and the invitation accept/reject flow                  |

> ⚠️ Never use real user accounts for testing.
> ⚠️ Sign Up tests (Patient, Guardian, Carer) generate unique emails automatically using `Date.now()` timestamps — no manual changes needed.

---

## Viewing Allure Reports

Clear previous results before a run you want a clean, single-suite report for:

```bash
Remove-Item -Recurse -Force allure-results
```

Then run your tests, and generate/open the report:

```bash
allure generate allure-results --clean -o allure-report
allure open allure-report
```

> Without clearing `allure-results` first, the report will include every run since the results folder was last cleared, not just your latest suite.

---

## Project Structure

```
avoora-automation/
├── .env.example              # Template — copy to .env and fill in your values
├── .env                      # Your local secrets — NOT committed to repo
├── .gitignore
├── package.json              # Dependencies and npm scripts
├── tsconfig.json             # TypeScript config
├── wdio.conf.ts              # Shared base config (timeouts, reporters, Mocha) — not runnable directly
├── wdio.android.conf.ts      # Android capabilities — reads from .env
├── wdio.ios.conf.ts          # iOS capabilities — reads from .env
└── test/
    ├── pageObjects/            # One class per screen — selectors and actions only
    │   ├── LoginPage.ts        # Login, Forgot Password, Logout (Patient/Guardian)
    │   ├── HomePage.ts         # Home screen, Add Appointment modal, Reminders widget
    │   ├── SignUpPage.ts       # Sign Up — Patient, Guardian, Carer roles; DOB, Password flows
    │   ├── ConsultationPage.ts # Consultation recording, AI summary, detail sections
    │   ├── HealthDiaryPage.ts  # Health Diary — voice notes, written notes, photo attachments
    │   ├── DocumentsPage.ts    # Document upload and list
    │   ├── ProfilePage.ts      # Profile, role verification, Change Email, Delete Account
    │   ├── CarerPage.ts        # Carer's My Patients screen — accepting/rejecting invitations, Carer-specific logout
    │   └── MembersPage.ts      # Patient's Settings > Members — sending invites, status badges, Carer removal
    ├── specs/                  # Test spec files — one per feature module
    │   ├── login.spec.ts
    │   ├── signup.spec.ts
    │   ├── guardian_signup.spec.ts
    │   ├── carer_signup.spec.ts
    │   ├── carer_login.spec.ts
    │   ├── carer_invitation.spec.ts
    │   ├── home.spec.ts
    │   ├── consultation.spec.ts
    │   ├── health_diary.spec.ts
    │   ├── documents.spec.ts
    │   ├── phase2_home.spec.ts
    │   ├── phase2_profile.spec.ts
    │   ├── phase2_carer.spec.ts
    │   ├── phase2_health_diary.spec.ts
    │   └── phase2_delete_account.spec.ts
    └── utils/
        └── audioPlayer.ts      # Injects real audio into the emulator mic for Consultation/Health Diary
```

---

## Key Principles

### Page Object Model (POM)

All element selectors and actions live in `test/pageObjects/`. Spec files never contain selectors.

```typescript
// ✅ Correct — selector in page object
class LoginPage {
  get emailField() {
    return $$("android.widget.EditText")[0];
  }
}

// ❌ Wrong — selector in spec file
it("test", async () => {
  const field = await $$("android.widget.EditText")[0]; // don't do this
});
```

### Use Accessibility IDs

Prefer `~AccessibilityLabel` over XPath — more stable across app updates.

```typescript
// ✅ Preferred
$("~Login");

// ⚠️ Use only as last resort
$('//android.widget.Button[@content-desc="Login"]');
```

### Watch for Accessibility ID Collisions Across Roles/Screens

The same label (e.g. "Settings") can point to completely different elements depending on which screen or role you're on — accessibility ids aren't guaranteed unique app-wide, only unambiguous on the specific screen you inspected them on.

```typescript
// ❌ Discovered the hard way: on the Patient's Members screen, the
// connected-carer card has its OWN "Settings" button (opens that carer's
// management page) sharing the exact same accessibility id as the app's
// global Settings icon. Calling this while on Members taps the wrong one.
await $("~Settings").click();

// ✅ Navigate back to a screen where the label is unambiguous first
await MembersPage.goBack();
await LoginPage.logout();
```

Also watch for the opposite problem — a button with **no** accessibility label at all. The Carer's My Patients gear icon has an empty `content-desc`, unlike Patient/Guardian's labeled Home equivalent, so it needs its own selector (`CarerPage.settingsButton`, anchored to the labeled "Notifications" button beside it) and its own `CarerPage.logout()` method.

### Use Waits, Not Pauses (Where Possible)

```typescript
// ❌ Avoid where a real wait condition exists
await browser.pause(5000);

// ✅ Prefer this — waits only as long as needed
await element.waitForDisplayed({ timeout: 10000 });
```

> Note: a short fixed pause after `terminateApp`/`activateApp` in `beforeEach` is intentional — the app's cold-start render isn't reliably detectable via `waitForDisplayed` alone.

### Credentials in .env Only

```typescript
// ❌ Never hardcode
await LoginPage.login("test@example.com", "Password123");

// ✅ Always use .env
await LoginPage.login(process.env.TEST_EMAIL, process.env.TEST_PASSWORD);
```

### Unique Test Data for Sign Up

```typescript
// ✅ Always generate unique emails (and phone numbers, where applicable) for Sign Up tests
const timestamp = Date.now();
const TEST_EMAIL = `avooratest.signup${timestamp}@mailinator.com`;
```

> Guardian/Carer sign-up caught a real gap here: reusing a fixed phone number that
> was already tied to a real registered account silently blocked progression past
> the Personal Details screen. Generate a unique phone number per run the same way
> as the email, rather than reusing one from manual testing.

### Destructive Actions Need a Disposable Account

Never run account-deletion or similarly destructive flows against `avooratest1` or another shared account. Check whether the flow is genuinely destructive at the point it's tested — some multi-step flows (e.g. Delete Account) only take effect after a separate confirmation step (like OTP entry), so confirm exactly where the point of no return is before assuming a disposable account is required.

### Multi-Account Flows: Plan the Logout/Login Sequence Carefully

Flows spanning two accounts (e.g. Patient invites, Carer accepts) need explicit logout/login at each handoff — don't assume `afterEach` will clean up correctly, since it may run from a screen where the standard logout selector doesn't apply (see the accessibility id collision note above). Have each test log out explicitly at its own natural end point, from a screen you've confirmed is safe, and treat `afterEach` as a safety net only.

---

## Adding New Tests

### 1. Inspect elements in Appium Inspector

Start Appium, open Inspector, connect to your device and identify `content-desc` values.

### 2. Add a Page Object

Create `test/pageObjects/YourPage.ts` with selectors as `get` properties and actions as `async` methods.

### 3. Add a spec file

Create `test/specs/yourfeature.spec.ts`, import the page object and write `describe`/`it` blocks.

### 4. Run it directly

```bash
npx wdio run wdio.android.conf.ts --spec test/specs/yourfeature.spec.ts
```

---

## Appium Inspector — Quick Connect

Use these capabilities to connect Inspector to the emulator:

```json
{
  "platformName": "Android",
  "appium:automationName": "UiAutomator2",
  "appium:deviceName": "emulator-5554",
  "appium:udid": "emulator-5554",
  "appium:appPackage": "com.avoora.health.stage",
  "appium:appActivity": "com.avoora.health.MainActivity",
  "appium:noReset": true
}
```

---

## Troubleshooting

| Problem                                                                                | Fix                                                                                                                                                                                                                                                                                                           |
| -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `No capabilities property found in wdio.conf.ts`                                       | You ran against the base config directly. Use `wdio.android.conf.ts` instead.                                                                                                                                                                                                                                 |
| `adb: device not found`                                                                | Run `adb kill-server && adb start-server`, check emulator is running                                                                                                                                                                                                                                          |
| `ANDROID_HOME not set`                                                                 | Add `ANDROID_HOME` to System Environment Variables                                                                                                                                                                                                                                                            |
| `Could not connect to Appium`                                                          | Run `appium` in a terminal first. Check `APPIUM_PORT=4723` in `.env`                                                                                                                                                                                                                                          |
| `App not found / Activity does not exist`                                              | Reinstall APK: `adb install -r path\to\app.apk`. Use full activity name `com.avoora.health.MainActivity`                                                                                                                                                                                                      |
| `Element not found`                                                                    | Selector may have changed after a new APK build. Re-inspect in Appium Inspector                                                                                                                                                                                                                               |
| `Emulator offline` or app never launches                                               | Run `adb kill-server && adb start-server`. If still stuck, cold-boot the emulator (`emulator -avd <name> -no-snapshot-load`) rather than resuming a snapshot                                                                                                                                                  |
| `System UI isn't responding` / emulator freezes                                        | Usually CPU contention. Turn off Window/Transition/Animator scales in Developer Options first. If "Close app" on the ANR dialog doesn't recover it, force-kill via `adb -s emulator-5554 emu kill` (or Task Manager) and Cold Boot the AVD from Android Studio's Device Manager rather than a normal relaunch |
| `getPageSource` timeout in Appium Inspector                                            | Usually the same root cause as the System UI freeze above — confirm the device is idle and animations are off, then restart the Inspector session                                                                                                                                                             |
| `element ("~Log out") still not displayed` right after an action on the Members screen | You're likely still on the Members screen, where its connected-carer card has its own "Settings" button sharing the same accessibility id as the global one. Call `MembersPage.goBack()` before `LoginPage.logout()`                                                                                          |
| `LoginPage.logout()` times out from the Carer's My Patients screen                     | That gear icon has no accessibility label. Use `CarerPage.logout()` instead, which anchors to the labeled "Notifications" button beside it                                                                                                                                                                    |
| `npm install fails`                                                                    | Delete `node_modules` and `package-lock.json`, then run `npm install` again                                                                                                                                                                                                                                   |
| `Sign Up tests crash on phone number`                                                  | Use `setValue('412345678')` without leading 0 — the +61 prefix is already in the field                                                                                                                                                                                                                        |
| Test passes in isolation but fails in full suite run                                   | Check whether a shared account (e.g. `avooratest1`) has accumulated state from other tests that changes the app's behaviour (e.g. appointment type already selected, existing reminders). Consider a dedicated account instead.                                                                               |
| iOS: `XCUITest driver error`                                                           | Ensure Xcode installed, device trusted on Mac, provisioning profile set up                                                                                                                                                                                                                                    |

---

## Known Blockers

| Blocker                                         | Impact                                                                                                                                     | Status                     |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------- |
| iOS requires macOS / Xcode                      | iOS automation not possible on Windows                                                                                                     | Open — pending Mac access  |
| Real audio/OTP/email verification               | Cannot verify actual OTP codes, email inbox content, or audio output — flows requiring these stop at the point real access would be needed | Open — inherent constraint |
| Camera-based flows (photo capture, QR scanning) | Not automatable via UI automation                                                                                                          | Open — inherent constraint |
| Real payment/subscription transactions          | Cannot automate actual purchase flows without a payment sandbox                                                                            | Open — inherent constraint |
| Offline/airplane-mode testing                   | Needs dedicated network-toggle test infrastructure, not yet built                                                                          | Open                       |

---

## Repository

🔗 https://github.com/iclickonline/avoora-test-automation

---
