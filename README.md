# Privacy Policy for Priority Task Manager

**Last Updated: September 26, 2026**

> **When this policy applies.** This version takes effect when **Aria**, the optional phone-call subscription, launches in the App Store. Until then, the version of the app in the App Store works as described in [The current version](#the-current-version-before-aria-launches) at the end: your tasks stay on your device and in your own iCloud account, and nothing is sent to us.

## Introduction

Priority Task Manager ("we", "our", or "the app") is a to-do app for iPhone and iPad. This policy explains, in plain words, what the app stores, what stays on your device, what is kept in your own iCloud account, and what is sent off your device if you choose to use Aria.

## Developer Information

**Developer:** Alonso Bardales
**Contact:** alonsobardales.apps@gmail.com
**App Name:** Priority Task Manager
**Bundle ID:** com.alonsobardales.PriorityTaskManager

## The Short Version

- There are no accounts. You never sign in to us.
- Your tasks live on your device and, if you are signed in to iCloud, in your own iCloud account. We cannot see them.
- The app sends none of your data to us unless you subscribe to Aria **and** agree in the app to share your data for calls.
- Aria sends your phone number, your first name, and the titles, priorities and dates of your incomplete tasks to our server so Aria can call you. The call goes through Twilio; the voice you talk to is OpenAI's.
- No analytics, no ads, no tracking, no data sales, and your data is never used to train AI models.

## What the App Stores on Your Device

- **Tasks:** titles, notes, priority (A to E), urgency and importance, due dates, scheduled dates and times, completion status.
- **Groups and schedules:** your recurring time blocks and the schedule tab.
- **Streaks and milestones.**
- **Settings:** onboarding status, reminder times, which calendars you chose to show, and, if you set up Aria, your first name, your phone number and your data-sharing choice.

This data is kept in the app's private storage on your device (iOS UserDefaults), protected by iOS and inaccessible to other apps.

**Sharing a schedule file.** If you use *Share Schedule*, the app creates a `.ptmschedule` file containing the schedules you picked and your device's name (for example "Alonso's iPhone"). It goes only where you send it, such as AirDrop or Messages.

## iCloud Sync (Your Own Account)

If your device is signed in to iCloud, the app mirrors a copy of your data to iCloud Key-Value storage **in your own Apple Account**, so your other devices stay in step:

- tasks and a list of recently deleted task IDs (so deletions reach your other devices)
- groups and schedules
- streaks and milestones
- a one-time copy of a few settings: your first name for Aria, your morning-reminder time and onboarding status

Your phone number is **not** synced to iCloud.

This copy is stored by Apple, encrypted in transit and on Apple's servers, and is available only to devices signed in to your Apple Account. It never goes to us. Apple's handling of it is covered by [Apple's privacy policy](https://www.apple.com/legal/privacy/).

**To turn it off:** open iOS **Settings**, tap your name, tap **iCloud**, and turn iCloud off for Priority Task Manager (or sign out of iCloud on the device). The app keeps working from the copy on your device. If you are not signed in to iCloud, nothing is synced.

## Calendar Access (Read, Not Stored)

If you allow it, the app reads your Apple Calendar events to draw them behind the day timeline so you can see tasks next to meetings.

- Events are read when a day is shown and held in memory only. They are never saved to the app's storage, to iCloud, or to our server.
- Today the app only **reads** your calendar. If a future version adds writing to your calendar, we will ask before doing so and update this policy.
- Which calendars you chose to show is a setting saved on your device.
- You can change or revoke access at any time in iOS **Settings > Privacy & Security > Calendars**.

## On-Device AI (Nothing Is Sent)

- **Task parsing** ("dentist tomorrow at 3") uses Apple's on-device Foundation Models on iOS 26 devices with Apple Intelligence. On other devices it uses a built-in keyword parser. Either way, nothing leaves your device.
- **Remi**, the in-app coaching chat, also runs on Apple's on-device model. Conversations are not saved and are not sent anywhere.

## Aria (Optional Paid Subscription)

Aria is a phone call. To make one, the app has to send some of your data off your device. This section is the long form of the **How Aria Uses Your Data** screen in the app.

### Before anything is sent

Aria only works after all three of these:

1. You subscribe to Aria through Apple.
2. You read **How Aria Uses Your Data** in the app and tap **Agree and Continue**. If you tap **Not Now**, nothing is sent, and you can read the screen again any time in **Settings > Aria**.
3. You verify your phone number. The app sends your number and your subscription receipt to our server; our server texts you a six-digit code through Twilio; you enter it in the app. Codes expire after 10 minutes and are held only as a hash in our server's memory. Aria can only ever call the number you verified.

### What is sent, each time a call is placed or scheduled

- Your **phone number**, so Aria can call you.
- Your **first name**, if you entered one in Settings.
- Your **incomplete tasks**: their titles, priorities, due dates, scheduled dates and times, and internal task IDs. Task **notes** and **completed tasks** are not sent.
- Your **time zone** and the **current time**, so Aria knows when "today" and "tomorrow" are.
- **Proof of your subscription**: the signed App Store receipt, so our server knows the call is paid for.

This applies to calls you start ("Call Me Now"), calls you schedule for a task, and the **Daily Check-in** call the app places for you at the time you set.

### Who receives it and why

- **Our Aria server**, hosted on Railway. It checks your subscription with Apple, confirms your number is verified, places the call, holds your task list for the length of the call, and records the changes Aria makes so your phone can pick them up.
- **Twilio**, which places the phone call to your number and sends you texts (the verification code, and a text if we have to end a call early because your daily allowance is used up). See [Twilio's privacy policy](https://www.twilio.com/en-us/legal/privacy).
- **OpenAI**, whose Realtime voice model is the voice you talk to. During the call our server gives it your first name, your task list, your time zone and the current time, and streams the conversation audio both ways. See [OpenAI's privacy policy](https://openai.com/policies/privacy-policy/) and its API data-usage terms for how OpenAI handles that audio; we do not control OpenAI's retention.
- **Apple**, which handles the subscription payment. We never see your payment details, only the signed receipt.

We do not use your data to train AI models, we do not use it for advertising, and we never sell it.

### What comes back

Changes Aria makes on the call, such as marking a task done, changing a priority, scheduling a task or adding a new one, are sent back to your phone and applied to your tasks there, like any edit you make yourself.

### What our server keeps, and for how long

| Data | Kept for |
|---|---|
| The call set-up data for one call (your name, task list, number, time zone) | Deleted 90 seconds after it is created if the call never connects, and when the call ends. The list of changes Aria made is kept for about one hour so your phone can fetch it, then deleted. |
| A scheduled call (your number, the task's title and ID, the call time) | Until the call fires or you cancel it. A call more than 24 hours overdue is dropped. |
| Your verified phone number, paired with your Aria subscription ID (Apple's transaction identifier; not your name or Apple ID) | Until you verify a different number, or ask us to delete it. |
| Daily call counters, by phone number and by subscription | About one day. |
| The result of checking your subscription with Apple | 10 minutes. |
| Server logs | Log lines record that a call or verification happened, with only the last four digits of your number. Tokens are redacted, and task titles are kept out of logs unless a debugging setting is turned on, which is off by default. Logs are kept by Railway under its own retention. |

**We do not keep:** call audio (it streams through our server and is never recorded or written to disk), transcripts, verification codes, or the receipt itself.

While a call is in progress, we can see that a call is active, the number and its duration, and we can end it. We have no way to listen in.

### Records from a retired promotion

An earlier version of the app offered a free month in return for a review and collected a name and email address for that. The offer has been withdrawn, the app no longer has the form, and the server no longer accepts submissions. The records that were collected are being deleted. If you sent one and would like confirmation, email us.

## Purchases

Full Access, Remi and Aria are bought through Apple. Apple handles payment and we never see your card or Apple ID. For Aria only, the app sends the signed App Store receipt to our server to confirm the subscription is active. Nothing about Full Access or Remi purchases leaves your device.

## Notifications

Reminders and Aria call alerts are local notifications generated on your device. We run no push-notification server and no notification data is sent to us.

## What We Do Not Do

- No analytics or usage tracking.
- No advertising and no advertising identifiers.
- No third-party SDKs in the app. Apart from Apple's own services (iCloud, the App Store), the only server the app talks to is our Aria server. When you open the Aria screen, the app asks that server whether Aria is available; that request carries no personal data.
- No selling, renting or trading of your data.
- No AI training on your data.
- No accounts, so nothing is linked to an identity we hold. Our server knows a subscription ID and a phone number, nothing more about who you are.

## Your Choices and How to Delete Your Data

**On your device**
- Delete any task from its detail screen.
- **Settings > Clear All Data** erases your tasks, schedules and streaks on this device and pushes the task and schedule deletions to iCloud, so your other devices clear too.
- Uninstalling the app removes everything stored on the device. The iCloud copy stays in your iCloud account until you clear it, so use **Clear All Data** first if you want it gone.

**iCloud**
- Turn off iCloud for the app or sign out, as described under [iCloud Sync](#icloud-sync-your-own-account).

**Calendar**
- Revoke access in iOS **Settings > Privacy & Security > Calendars**.

**Aria**
- Remove your phone number in **Settings > Aria**. Aria cannot call you without it, and nothing more is sent.
- Turn off **Daily Check-in** and cancel any scheduled calls to stop calls you set up earlier.
- Cancel the subscription in your Apple subscription settings.

**On our server**
- Email **alonsobardales.apps@gmail.com** from any address and include the phone number you verified. We will reply within 48 hours and delete your verified number, any scheduled calls and your call counters within 30 days; in practice it is usually done within a few days. Call data is deleted automatically within about an hour of each call, so there is nothing else to remove.

## Children's Privacy

Priority Task Manager is not directed at children under 13, and we do not knowingly collect personal information from them. Aria requires a verified phone number and a paid subscription. If you believe a child under 13 has given us a phone number, email us and we will delete it.

## Changes to This Policy

We may update this policy from time to time. Changes are shown by the "Last Updated" date at the top of this page. If we change what Aria sends or who receives it, the app will show you the updated **How Aria Uses Your Data** screen and ask you to agree again before anything more is sent.

## Your Consent

By using Priority Task Manager, you agree to this policy. Data is sent for Aria only after you agree to it separately in the app.

## Contact Us

If you have questions about this policy or want your data deleted:

**Email:** alonsobardales.apps@gmail.com
**Response Time:** Within 48 hours

## The Current Version (Before Aria Launches)

The version of the app in the App Store today does not include Aria, and no feature in it sends data to us. Your tasks are stored on your device and, if you are signed in to iCloud, in your own iCloud account, exactly as described under [What the App Stores on Your Device](#what-the-app-stores-on-your-device) and [iCloud Sync](#icloud-sync-your-own-account). The Aria section applies only once Aria launches and only if you choose to use it.

---

| | |
|---|---|
| **Data Storage** | On your device, plus your own iCloud account if you are signed in |
| **Data Sent to Us** | None of your data, unless you subscribe to Aria and agree in the app |
| **What Aria Sends** | Phone number, first name, incomplete task titles/priorities/dates, time zone, App Store receipt |
| **Who Receives It** | Our server on Railway, Twilio (call and texts), OpenAI (voice) |
| **Kept on Our Server** | Verified number paired with subscription ID until you change it or ask; call data for about an hour after a call; scheduled calls until they fire |
| **Call Audio** | Streams through; never recorded by us |
| **Third-Party SDKs in the App** | None |
| **Analytics** | None |
| **Advertising** | None |
| **Tracking** | None |
| **AI Training on Your Data** | Never |
| **Data Sold** | Never |
| **Delete Your Data** | Settings > Clear All Data; remove your number in Settings > Aria; uninstall; email us for server-side deletion |
