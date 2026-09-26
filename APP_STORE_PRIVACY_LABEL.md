# App Store Connect: Privacy Label and Listing Changes for the Aria Launch

One page for filling in **App Privacy** in App Store Connect and fixing the listing before `FeatureFlags.ariaEnabled` flips. Everything below is taken from the shipped privacy manifest (`PriorityTaskManager/PriorityTaskManager/PrivacyInfo.xcprivacy` on iOS `main`), the request bodies in `Models/AriaManager.swift`, and the backend `AriaBackend/server.js`. The public policy is the `README.md` in this repo, published at https://alonbbar6.github.io/prioritytaskmanager-privacy/.

## 1. Data types to declare

Answer **"Yes, we collect data from this app"**, then declare exactly these. Every row is **Data Linked to You** and **not used for tracking**, with the single purpose **App Functionality**. "Linked" is Apple's default unless direct identifiers are stripped before collection and the data is never tied to other datasets; our server stores the phone number (a direct identifier) paired with the subscription's transaction ID and receives the first name, the task titles and the call audio alongside that number, so "not linked" is not defensible even though there are no accounts.

| App Store Connect category | Data type | Collected | Linked to identity | Used for tracking | Purpose | Manifest entry | Where it is sent (code) |
|---|---|---|---|---|---|---|---|
| Contact Info | **Phone Number** | Yes | Yes | No | App Functionality | `NSPrivacyCollectedDataTypePhoneNumber` | `phone` in `/verify-phone/*`, `/call`, `/call-briefing`, `/schedule-call` (AriaManager.swift) |
| Contact Info | **Name** | Yes | Yes | No | App Functionality | `NSPrivacyCollectedDataTypeName` | `userName` (first name from Settings) in the three call routes |
| User Content | **Other User Content** (task titles, priorities, due and scheduled dates, task IDs) | Yes | Yes | No | App Functionality | `NSPrivacyCollectedDataTypeOtherUserContent` (added on iOS `main`, 3ad873f) | `allTasks` / `tasks` arrays in the three call routes; forwarded to OpenAI in the system prompt (server.js `buildSystemPrompt`) |
| User Content | **Audio Data** (the call audio) | Yes | Yes | No | App Functionality | `NSPrivacyCollectedDataTypeAudioData` (added on iOS `main`, 3ad873f) | The app never captures audio; the call is on the phone line. Twilio streams it to our server, which streams it to OpenAI's Realtime API and back (server.js `/stream`). We never write it to disk, but OpenAI's API terms allow abuse-monitoring logs for up to 30 days, which meets Apple's definition of "collect" (a third-party partner can access it beyond servicing the request in real time). |
| Purchases | **Purchase History** | Yes | Yes | No | App Functionality | `NSPrivacyCollectedDataTypePurchaseHistory` | `transactionJws` (signed App Store transaction) on every request that verifies a number or places or schedules a call, and on the launch-time `/verify-phone/status` check; verified with Apple server-side. Not on `/aria-status`, cancel, check-schedule or fetch-actions, which carry only per-call bearer tokens. |
| Contact Info | **Email Address** | Yes, **only until the legacy review-claim records are deleted** | Yes | No | App Functionality | `NSPrivacyCollectedDataTypeEmailAddress` (kept as legacy, linked false) | The live App Store build (3.7) still contains the form; the next version removes it, and `POST /review-claim` answers 410 once the new server build is deployed. The stored records hold name, email and the review text (summary, pros, cons). Remove this row **and** the manifest entry once `aria_review_claims.json` on the Railway volume is deleted. |

Do **not** declare: Usage Data, Diagnostics, Location, Identifiers, Contacts, Photos, Health, Financial Info, Browsing or Search History. The app has no analytics, no crash reporter, no ad SDK and no third-party SDK; the only network destination is the Aria backend. Time zone is sent but is not a location data type. The device's IP address reaches the hosting provider on every request like any web call and is not retained by our code, which Apple's guidance exempts.

Manifest facts the label must agree with:
- `NSPrivacyTracking` is `false` and `NSPrivacyTrackingDomains` is empty. Answer **No** to tracking.
- The only accessed-API reason is `NSPrivacyAccessedAPICategoryUserDefaults` / `CA92.1` (app's own defaults). No other required-reason APIs.
- The current live label ("Data Not Linked to You: User Content, Usage Data") is wrong three times: **Usage Data is not collected**, **Phone Number, Name, Audio Data and Purchase History are missing**, and the rows should be **Linked to You**.

Manifest edits in the iOS repo (`PrivacyInfo.xcprivacy` on `main`, commit 3ad873f; `PrivacyManifestTests` pin the set and the linked flags):
1. DONE: `NSPrivacyCollectedDataTypeOtherUserContent` dict added (linked **true**, tracking false, purpose App Functionality).
2. DONE: `NSPrivacyCollectedDataTypeAudioData` dict added (linked **true**, tracking false, purpose App Functionality).
3. DONE: `NSPrivacyCollectedDataTypeLinked` is **true** on Phone Number, Name and Purchase History. The legacy Email Address entry was left at linked false and is removed in step 5 rather than corrected.
4. DONE: the `Name` comment now reads "the first name from Settings, sent so Aria can greet the user on calls"; the Email Address comment marks it LEGACY.
5. REMAINING: delete the `EmailAddress` dict after the legacy records are gone (`aria_review_claims.json` exported and deleted).

Still to do outside the iOS repo: the App Store Connect label itself (the table above) and the listing copy in section 2.

Decisions recorded here (see the policy's Aria section for the user-facing wording):
- **Audio Data is declared.** Reason in the table row above. The policy's retention table now has a "Call audio" row saying the same.
- **Linked to You for every row.** Reason in the opening paragraph of this section.

## 2. Listing copy that must be removed or replaced

Search the App Store description, promotional text, keywords, screenshots and in-app purchase display names for these phrases and delete them. Each is now false.

| Phrase | Why it is false |
|---|---|
| "one-time download" (the on-device model) | Nothing is downloaded: task parsing uses Apple Foundation Models or a keyword parser. The unused downloader (`Models/LocalCoreMLAssistant.swift`, with a huggingface.co URL) was deleted from the iOS repo on `main` (c7e41f1); `ShippedAppComplianceTests` fails if the file, the host or the training-script references come back. |
| "never leaves your device" / "your data never leaves your device" | Tasks are mirrored to the user's iCloud account, and Aria sends task titles, phone number and name to Railway, Twilio and OpenAI. |
| "on-device voice, no cloud" (Aria) | Aria's voice is OpenAI's Realtime API reached through our server. Already removed from `AriaSubscriptionView` and `StoreKitConfig.storekit`; check the listing too. |
| "No data is transmitted over the internet", "no cloud databases", "no third-party services" (old policy text quoted in the listing or screenshots) | Replaced by the new policy. |

Replacement phrasing that is true: "Your tasks stay on your device and in your own iCloud. Aria, an optional subscription, calls you by phone; see How Aria Uses Your Data in the app."

Also update in App Store Connect:
- **Privacy Policy URL:** https://alonbbar6.github.io/prioritytaskmanager-privacy/ (unchanged URL, new content; the app's `LegalLinks.privacyPolicyURL` points here).
- **Terms of Use (EULA):** the app links Apple's standard EULA (`LegalLinks.termsOfUseURL`). Leave the custom EULA field empty or paste the same link in the description's "Terms of Use" line, which Apple requires for auto-renewing subscriptions.
- **Support URL:** the `support.md` page in this repo.

## 3. App Review notes to include

Paste into **App Review Information > Notes** for the first build with `ariaEnabled = true`.

> **Guideline 5.1.2(i), sharing data with a third-party AI.** Aria is an optional auto-renewing subscription that phones the user. Before the app sends anything off the device it shows "How Aria Uses Your Data" (`AriaConsentSheet`), which lists exactly what is sent (phone number, first name, titles/priorities/dates of incomplete tasks, time zone, App Store receipt), who receives it (our server on Railway, Twilio for the call and SMS, OpenAI's Realtime API for the voice) and why. The user must tap **Agree and Continue**; **Not Now** sends nothing and the app keeps working. The sheet cannot be swiped away before a choice, is re-readable at Settings > Aria > "How Aria uses your data", and any change to the disclosure bumps its version and forces re-consent (it is at version 2: the sheet now says that removing the number cancels scheduled calls, and that our server keeps the verified number until another is verified or deletion is requested by email). Privacy Policy and Terms of Use links appear on the sheet and on every paywall (Aria, Remi, Full Access) and in Settings > About. To withdraw, the user removes the phone number in Settings > Aria, which cancels every backend schedule, the pending background slot, the local call alerts and the Daily Check-in and clears the device's cached verified number (no new calls, no task data sent), then cancels the subscription; the policy says so in "Your Choices".
>
> **Phone verification.** Aria only calls a number the subscriber has proven they own. After consent, the app posts the number to `/verify-phone/start`; the server texts a six-digit code via Twilio (10-minute expiry, five attempts, rate-limited); `/verify-phone/check` binds that number to the subscription. All call routes return 403 for any other number. US numbers only (stated in the policy and the support FAQ).
>
> **What the server keeps.** Call context for a call placed now is deleted 90 s after creation if the call never connects and within about one hour of the call ending. A scheduled call keeps the number, task title and ID and fire time on disk, and the first name, task list and time zone in memory, until it fires or is cancelled (a call more than 24 h overdue is dropped; after a restart only the disk fields remain). The verified number is kept paired with the subscription's transaction ID until the user verifies another number or asks for deletion by email, including after the subscription ends. Call audio is streamed, never recorded by us; OpenAI's API terms allow abuse-monitoring logs for up to 30 days. Logs redact phone numbers to the last four digits, including inside provider (Twilio) error messages, which are also never echoed to the app; long tokens are dropped. Task titles, including those of tasks Aria adds or changes on a call, are kept out of logs unless `ARIA_LOG_TASK_TITLES=true`, which it is not in production. The per-phone call counter drops a number 24 h after its last call. Railway keeps logs for 7 days.
>
> **To test.** Subscribe to Aria with a sandbox account (products `com.alonsobardales.PriorityTaskManager.aria.monthly` / `.aria.annual`), open Settings > Aria, enter a first name and a US phone number you can receive SMS on, tap **Verify number**, enter the code, then **Call Me Now**. The "How Aria Uses Your Data" sheet appears before the first send. Declining it, or closing the verification sheet, leaves the app fully usable without Aria.
>
> **No accounts, no analytics, no tracking, no third-party SDKs.** All other features (tasks, schedules, Calendar overlay, on-device AI parsing, Remi) work entirely on device; Calendar events are read only and never stored; iCloud Key-Value storage syncs the user's own data to their own account.

Before submitting, confirm the items the notes assert: `STREAM_SECRET` set and `ADMIN_SECRET` rotated on Railway, `/health` returning `dataVolumeOk: true`, the Aria products live in App Store Connect (sandbox and production), the legacy review-claim records exported and deleted, and one real TestFlight call completed end to end (verify -> schedule -> call -> actions synced back).

Backend and iOS follow-ups the policy wording used to work around, all landed (backend `master` da063bf, iOS `main` c7e41f1); the policy now relies on them:
- `server.js`: the four `Task created/completed/Priority updated/Scheduled` log lines go through `logTaskTitle()`, so titles appear only with `ARIA_LOG_TASK_TITLES=true`; the policy no longer says logs "may include the titles of tasks Aria adds or changes".
- `server.js`: every path that awaits Twilio (/call, /call-briefing, the scheduled and restored-call timers, /admin/end-call, the stream cuts and the hang-up) logs `describeError()`, which cuts phone-like runs to `***NNNN` and drops 32+-character tokens, and the routes answer a generic sentence instead of `err.message`; the "may occasionally include the full number" caveat is gone.
- `server.js`: `prunePhoneCalls()` runs in `flushCounters()` and `loadCounters()`, so `aria_counters.json` names only numbers with a call in the last 24 h; the per-phone counter row says the entry is dropped a day after the last call.
- iOS: `StatsManager.resetAll()` removes `icloud_stats` and its timestamp, and Clear All Data calls `ICloudSync.removeSettingsSnapshot()`; the policy describes Clear All Data as clearing the whole iCloud copy, with the caveat that a device that has not synced yet can mirror its own streaks back (keys are removed, not zeroed, so that device's max-wins merge keeps its stats).
- iOS: `AriaManager.phoneNumberRemoved(tasks:)` cancels every backend schedule (DELETE /schedule-call/:id, best effort, no retry if offline), the background slot, the local notifications and the Daily Check-in, and clears the cached verified number; the consent sheet says removing the number "stops new calls and cancels any that are scheduled", its "Your verified number" section says the server keeps it until re-verification or an email request, and `AriaConsent.currentVersion` is 2. Still open: the sheet's intro does not mention the verification send that precedes any call; the policy covers it under "Before anything is sent".
