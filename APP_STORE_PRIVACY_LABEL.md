# App Store Connect: Privacy Label and Listing Changes for the Aria Launch

One page for filling in **App Privacy** in App Store Connect and fixing the listing before `FeatureFlags.ariaEnabled` flips. Everything below is taken from the shipped privacy manifest (`PriorityTaskManager/PriorityTaskManager/PrivacyInfo.xcprivacy` on iOS `main`), the request bodies in `Models/AriaManager.swift`, and the backend `AriaBackend/server.js`. The public policy is the `README.md` in this repo, published at https://alonbbar6.github.io/prioritytaskmanager-privacy/.

## 1. Data types to declare

Answer **"Yes, we collect data from this app"**, then declare exactly these. Every row is **Data Linked to You** and **not used for tracking**, with the single purpose **App Functionality**. "Linked" is Apple's default unless direct identifiers are stripped before collection and the data is never tied to other datasets; our server stores the phone number (a direct identifier) paired with the subscription's transaction ID and receives the first name, the task titles and the call audio alongside that number, so "not linked" is not defensible even though there are no accounts.

| App Store Connect category | Data type | Collected | Linked to identity | Used for tracking | Purpose | Manifest entry | Where it is sent (code) |
|---|---|---|---|---|---|---|---|
| Contact Info | **Phone Number** | Yes | Yes | No | App Functionality | `NSPrivacyCollectedDataTypePhoneNumber` | `phone` in `/verify-phone/*`, `/call`, `/call-briefing`, `/schedule-call` (AriaManager.swift) |
| Contact Info | **Name** | Yes | Yes | No | App Functionality | `NSPrivacyCollectedDataTypeName` | `userName` (first name from Settings) in the three call routes |
| User Content | **Other User Content** (task titles, priorities, due and scheduled dates, task IDs) | Yes | Yes | No | App Functionality | **Missing from the manifest today; add `NSPrivacyCollectedDataTypeOtherUserContent`** | `allTasks` / `tasks` arrays in the three call routes; forwarded to OpenAI in the system prompt (server.js `buildSystemPrompt`) |
| User Content | **Audio Data** (the call audio) | Yes | Yes | No | App Functionality | **Missing from the manifest today; add `NSPrivacyCollectedDataTypeAudioData`** | The app never captures audio; the call is on the phone line. Twilio streams it to our server, which streams it to OpenAI's Realtime API and back (server.js `/stream`). We never write it to disk, but OpenAI's API terms allow abuse-monitoring logs for up to 30 days, which meets Apple's definition of "collect" (a third-party partner can access it beyond servicing the request in real time). |
| Purchases | **Purchase History** | Yes | Yes | No | App Functionality | `NSPrivacyCollectedDataTypePurchaseHistory` | `transactionJws` (signed App Store transaction) on every request that verifies a number or places or schedules a call, and on the launch-time `/verify-phone/status` check; verified with Apple server-side. Not on `/aria-status`, cancel, check-schedule or fetch-actions, which carry only per-call bearer tokens. |
| Contact Info | **Email Address** | Yes, **only until the legacy review-claim records are deleted** | Yes | No | App Functionality | `NSPrivacyCollectedDataTypeEmailAddress` | No longer sent by the app (`POST /review-claim` answers 410). The stored records hold name, email and the review text (summary, pros, cons). Remove this row **and** the manifest entry once `aria_review_claims.json` on the Railway volume is deleted. |

Do **not** declare: Usage Data, Diagnostics, Location, Identifiers, Contacts, Photos, Health, Financial Info, Browsing or Search History. The app has no analytics, no crash reporter, no ad SDK and no third-party SDK; the only network destination is the Aria backend. Time zone is sent but is not a location data type. The device's IP address reaches the hosting provider on every request like any web call and is not retained by our code, which Apple's guidance exempts.

Manifest facts the label must agree with:
- `NSPrivacyTracking` is `false` and `NSPrivacyTrackingDomains` is empty. Answer **No** to tracking.
- The only accessed-API reason is `NSPrivacyAccessedAPICategoryUserDefaults` / `CA92.1` (app's own defaults). No other required-reason APIs.
- The current live label ("Data Not Linked to You: User Content, Usage Data") is wrong three times: **Usage Data is not collected**, **Phone Number, Name, Audio Data and Purchase History are missing**, and the rows should be **Linked to You**.

Manifest edits to make in the iOS repo alongside the label (not done here):
1. Add an `NSPrivacyCollectedDataTypeOtherUserContent` dict (linked **true**, tracking false, purpose App Functionality).
2. Add an `NSPrivacyCollectedDataTypeAudioData` dict (linked **true**, tracking false, purpose App Functionality).
3. Set `NSPrivacyCollectedDataTypeLinked` to **true** on the existing Phone Number, Name and Purchase History entries (and Email Address while it remains).
4. Update the comment on the `Name` entry: it now reads "the review-promo claim form (/review-claim) collects it"; the form is gone, Aria greeting is the only use.
5. Delete the `EmailAddress` dict after the legacy records are gone.

Decisions recorded here (see the policy's Aria section for the user-facing wording):
- **Audio Data is declared.** Reason in the table row above. The policy's retention table now has a "Call audio" row saying the same.
- **Linked to You for every row.** Reason in the opening paragraph of this section.

## 2. Listing copy that must be removed or replaced

Search the App Store description, promotional text, keywords, screenshots and in-app purchase display names for these phrases and delete them. Each is now false.

| Phrase | Why it is false |
|---|---|
| "one-time download" (the on-device model) | The on-device CoreML download is no longer used (`Models/LocalCoreMLAssistant.swift` remains in the tree with a huggingface.co URL but is never instantiated); task parsing uses Apple Foundation Models or a keyword parser, nothing is downloaded. Consider deleting the file in the iOS repo so a static scan does not find a non-Apple URL. |
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

> **Guideline 5.1.2(i), sharing data with a third-party AI.** Aria is an optional auto-renewing subscription that phones the user. Before the app sends anything off the device it shows "How Aria Uses Your Data" (`AriaConsentSheet`), which lists exactly what is sent (phone number, first name, titles/priorities/dates of incomplete tasks, time zone, App Store receipt), who receives it (our server on Railway, Twilio for the call and SMS, OpenAI's Realtime API for the voice) and why. The user must tap **Agree and Continue**; **Not Now** sends nothing and the app keeps working. The sheet cannot be swiped away before a choice, is re-readable at Settings > Aria > "How Aria uses your data", and any change to the disclosure bumps its version and forces re-consent. Privacy Policy and Terms of Use links appear on the sheet and on every paywall (Aria, Remi, Full Access) and in Settings > About. To withdraw, the user removes the phone number in Settings > Aria (no new calls, no task data sent), cancels any scheduled calls, and cancels the subscription; the policy says so in "Your Choices".
>
> **Phone verification.** Aria only calls a number the subscriber has proven they own. After consent, the app posts the number to `/verify-phone/start`; the server texts a six-digit code via Twilio (10-minute expiry, five attempts, rate-limited); `/verify-phone/check` binds that number to the subscription. All call routes return 403 for any other number. US numbers only (stated in the policy and the support FAQ).
>
> **What the server keeps.** Call context for a call placed now is deleted 90 s after creation if the call never connects and within about one hour of the call ending. A scheduled call keeps the number, task title and ID and fire time on disk, and the first name, task list and time zone in memory, until it fires or is cancelled (a call more than 24 h overdue is dropped; after a restart only the disk fields remain). The verified number is kept paired with the subscription's transaction ID until the user verifies another number or asks for deletion by email, including after the subscription ends. Call audio is streamed, never recorded by us; OpenAI's API terms allow abuse-monitoring logs for up to 30 days. Logs redact phone numbers to the last four digits (a raw provider error message may occasionally include a full number) and may include the titles of tasks Aria adds or changes on a call; other task titles are kept out of logs unless a debugging flag is on, which it is not in production.
>
> **To test.** Subscribe to Aria with a sandbox account (products `com.alonsobardales.PriorityTaskManager.aria.monthly` / `.aria.annual`), open Settings > Aria, enter a first name and a US phone number you can receive SMS on, tap **Verify number**, enter the code, then **Call Me Now**. The "How Aria Uses Your Data" sheet appears before the first send. Declining it, or closing the verification sheet, leaves the app fully usable without Aria.
>
> **No accounts, no analytics, no tracking, no third-party SDKs.** All other features (tasks, schedules, Calendar overlay, on-device AI parsing, Remi) work entirely on device; Calendar events are read only and never stored; iCloud Key-Value storage syncs the user's own data to their own account.

Before submitting, confirm the items the notes assert: `STREAM_SECRET` set and `ADMIN_SECRET` rotated on Railway, `/health` returning `dataVolumeOk: true`, the Aria products live in App Store Connect (sandbox and production), the legacy review-claim records exported and deleted, and one real TestFlight call completed end to end (verify -> schedule -> call -> actions synced back).

Backend and iOS follow-ups the policy wording currently works around (fixing them lets the policy say less):
- `server.js` ~1897-1915: the four `Task created/completed/Priority updated/Scheduled` log lines print task titles unconditionally; route them through `logTaskTitle()` and the policy can drop "may include the titles of tasks Aria adds or changes".
- `server.js` /call, /call-briefing, scheduled and restored-call catch blocks and admin end-call log `err.message` raw; pass them through `redactForLog()` and the "may occasionally include the full number" caveat can go.
- `server.js` `flushCounters`: prune `phoneCalls` entries with no timestamps in the last 24 h, then the per-phone counter row can say "about one day".
- iOS `StatsManager.resetAll()` never mirrors the reset to `icloud_stats`, and nothing clears `icloud_settings`; fix both and Clear All Data can be described as clearing the whole iCloud copy.
- iOS: clearing the phone number in Settings does not cancel server-side schedules; either cancel them on clear or keep the consent sheet's line 36 ("Aria can't call you without your phone number") in step with the policy when the disclosure version is next bumped. The sheet's intro should also mention the verification send that precedes any call.
