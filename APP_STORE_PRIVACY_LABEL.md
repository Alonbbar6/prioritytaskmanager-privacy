# App Store Connect: Privacy Label and Listing Changes for the Aria Launch

One page for filling in **App Privacy** in App Store Connect and fixing the listing before `FeatureFlags.ariaEnabled` flips. Everything below is taken from the shipped privacy manifest (`PriorityTaskManager/PriorityTaskManager/PrivacyInfo.xcprivacy` on iOS `main`), the request bodies in `Models/AriaManager.swift`, and the backend `AriaBackend/server.js`. The public policy is the `README.md` in this repo, published at https://alonbbar6.github.io/prioritytaskmanager-privacy/.

## 1. Data types to declare

Answer **"Yes, we collect data from this app"**, then declare exactly these. Every row is **Data Not Linked to You** (there are no accounts; the server holds a subscription transaction ID and a phone number, nothing that identifies a person by name or Apple ID) and **not used for tracking**, with the single purpose **App Functionality**.

| App Store Connect category | Data type | Collected | Linked to identity | Used for tracking | Purpose | Manifest entry | Where it is sent (code) |
|---|---|---|---|---|---|---|---|
| Contact Info | **Phone Number** | Yes | No | No | App Functionality | `NSPrivacyCollectedDataTypePhoneNumber` | `phone` in `/verify-phone/*`, `/call`, `/call-briefing`, `/schedule-call` (AriaManager.swift) |
| Contact Info | **Name** | Yes | No | No | App Functionality | `NSPrivacyCollectedDataTypeName` | `userName` (first name from Settings) in the three call routes |
| User Content | **Other User Content** (task titles, priorities, due and scheduled dates, task IDs) | Yes | No | No | App Functionality | **Missing from the manifest today; add `NSPrivacyCollectedDataTypeOtherUserContent`** | `allTasks` / `tasks` arrays in the three call routes; forwarded to OpenAI in the system prompt (server.js `buildSystemPrompt`) |
| Purchases | **Purchase History** | Yes | No | No | App Functionality | `NSPrivacyCollectedDataTypePurchaseHistory` | `transactionJws` (signed App Store transaction) on every Aria request; verified with Apple server-side |
| Contact Info | **Email Address** | Yes, **only until the legacy review-claim records are deleted** | No | No | App Functionality | `NSPrivacyCollectedDataTypeEmailAddress` | No longer sent by the app (`POST /review-claim` answers 410). Remove this row **and** the manifest entry once `aria_review_claims.json` on the Railway volume is deleted. |

Do **not** declare: Usage Data, Diagnostics, Location, Identifiers, Contacts, Photos, Health, Financial Info, Browsing or Search History. The app has no analytics, no crash reporter, no ad SDK and no third-party SDK; the only network destination is the Aria backend. Time zone is sent but is not a location data type.

Manifest facts the label must agree with:
- `NSPrivacyTracking` is `false` and `NSPrivacyTrackingDomains` is empty. Answer **No** to tracking.
- The only accessed-API reason is `NSPrivacyAccessedAPICategoryUserDefaults` / `CA92.1` (app's own defaults). No other required-reason APIs.
- The current live label ("Data Not Linked to You: User Content, Usage Data") is wrong twice: **Usage Data is not collected** and **Phone Number, Name and Purchase History are missing**.

Manifest edits to make in the iOS repo alongside the label (not done here):
1. Add an `NSPrivacyCollectedDataTypeOtherUserContent` dict (linked false, tracking false, purpose App Functionality).
2. Update the comment on the `Name` entry: it now reads "the review-promo claim form (/review-claim) collects it"; the form is gone, Aria greeting is the only use.
3. Delete the `EmailAddress` dict after the legacy records are gone.

Judgment calls for Alonso (see the policy's Aria section):
- **Audio Data.** Call audio streams Twilio -> our server -> OpenAI in real time and is never written to disk by us. The app itself never captures audio (the call is on the phone line). Apple's definition of "collect" is data transmitted off the device and retained beyond servicing the request; declaring **Audio Data** is the conservative choice. Whichever way you decide, say it in the review notes.
- **"Not linked."** The server pairs the verified phone number with the Aria subscription transaction ID and receives task titles alongside that number. If you would rather not defend "not linked" for Phone Number, mark the rows as **Data Linked to You**; nothing else changes.

## 2. Listing copy that must be removed or replaced

Search the App Store description, promotional text, keywords, screenshots and in-app purchase display names for these phrases and delete them. Each is now false.

| Phrase | Why it is false |
|---|---|
| "one-time download" (the on-device model) | The on-device CoreML download path was removed (commit `6e5fb7a`); task parsing uses Apple Foundation Models or a keyword parser, nothing is downloaded. |
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

> **Guideline 5.1.2(i), sharing data with a third-party AI.** Aria is an optional auto-renewing subscription that phones the user. Before the app sends anything off the device it shows "How Aria Uses Your Data" (`AriaConsentSheet`), which lists exactly what is sent (phone number, first name, titles/priorities/dates of incomplete tasks, time zone, App Store receipt), who receives it (our server on Railway, Twilio for the call and SMS, OpenAI's Realtime API for the voice) and why. The user must tap **Agree and Continue**; **Not Now** sends nothing and the app keeps working. The sheet cannot be swiped away before a choice, is re-readable at Settings > Aria > "How Aria uses your data", and any change to the disclosure bumps its version and forces re-consent. Privacy Policy and Terms of Use links appear on the sheet and on every paywall (Aria, Remi, Full Access) and in Settings > About.
>
> **Phone verification.** Aria only calls a number the subscriber has proven they own. After consent, the app posts the number to `/verify-phone/start`; the server texts a six-digit code via Twilio (10-minute expiry, five attempts, rate-limited); `/verify-phone/check` binds that number to the subscription. All call routes return 403 for any other number. US numbers only.
>
> **What the server keeps.** Call context is deleted 90 s after creation if the call never connects and within about one hour of the call ending; scheduled calls are kept until they fire or are cancelled; the verified number is kept paired with the subscription's transaction ID until the user verifies another number or asks for deletion by email. Call audio is streamed, never recorded. Logs redact phone numbers to the last four digits and never contain task text.
>
> **To test.** Subscribe to Aria with a sandbox account (products `com.alonsobardales.PriorityTaskManager.aria.monthly` / `.aria.annual`), open Settings > Aria, enter a first name and a US phone number you can receive SMS on, tap **Verify number**, enter the code, then **Call Me Now**. The "How Aria Uses Your Data" sheet appears before the first send. Declining it, or closing the verification sheet, leaves the app fully usable without Aria.
>
> **No accounts, no analytics, no tracking, no third-party SDKs.** All other features (tasks, schedules, Calendar overlay, on-device AI parsing, Remi) work entirely on device; Calendar events are read only and never stored; iCloud Key-Value storage syncs the user's own data to their own account.

Before submitting, confirm the items the notes assert: `STREAM_SECRET` set and `ADMIN_SECRET` rotated on Railway, `/health` returning `dataVolumeOk: true`, the Aria products live in App Store Connect (sandbox and production), and one real TestFlight call completed end to end (verify -> schedule -> call -> actions synced back).
