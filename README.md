# Privacy Policy for Priority Task Manager

**Last Updated: September 27, 2026**

> **Aria may not yet be available in the version of the app you have.** If it is not, the Aria section does not apply to you yet; everything else does. See [If your version does not have Aria yet](#if-your-version-does-not-have-aria-yet) at the end.

## Introduction

Priority Task Manager ("we", "our", or "the app") is a to-do app for iPhone and iPad. This policy explains, in plain words, what the app stores, what stays on your device, what is kept in your own iCloud account, and what is sent off your device if you choose to use Aria.

## Developer Information

**Developer:** Alonso Bryan Bardales, independent developer, United States
**Contact:** alonsobardales.apps@gmail.com
**App Name:** Priority Task Manager
**Bundle ID:** com.alonsobardales.PriorityTaskManager

## The Short Version

- There are no accounts. You never sign in to us.
- Your tasks live on your device and, if you are signed in to iCloud, in your own iCloud account. We cannot see them.
- The app sends none of your data to us unless you subscribe to Aria **and** agree in the app to share your data for calls.
- Aria sends your phone number, your first name, the titles, priorities and dates of your incomplete tasks (up to 50 of them), your time zone and your App Store receipt to our server so Aria can call you. The call goes through Twilio; the voice you talk to is OpenAI's.
- No analytics, no ads, no tracking, no data sales. We never use your data to train AI models, and OpenAI's API terms say the same for data sent through the API.

## What the App Stores on Your Device

- **Tasks:** titles, notes, priority (A to E), urgency and importance, due dates, scheduled dates and times, completion status.
- **Groups and schedules:** your recurring time blocks and the schedule tab.
- **Streaks and milestones.**
- **Settings:** onboarding status, reminder times, which calendars you chose to show, and, if you set up Aria, your first name, your phone number and your data-sharing choice.

This data is kept in the app's private storage on your device (iOS UserDefaults), protected by iOS and inaccessible to other apps. Two small items live in the device Keychain instead: the date you first installed the app (used to time the free trial) and, if you use Aria, the random access codes our server hands out for your most recent call and for each call you have scheduled, which the app presents back to our server to fetch a call's changes or cancel a scheduled call. Neither moves to another device through a backup.

**Sharing a schedule file.** If you use *Share Schedule*, the app creates a `.ptmschedule` file containing the schedules you picked and your device's name (for example "Alonso's iPhone"). It goes only where you send it, such as AirDrop or Messages.

## iCloud Sync (Your Own Account)

If your device is signed in to iCloud, the app mirrors a copy of your data to iCloud Key-Value storage **in your own Apple Account**, so your other devices stay in step:

- tasks and a list of recently deleted task IDs (so deletions reach your other devices)
- groups and schedules, and a list of recently deleted group IDs
- streaks and milestones, and the time you last cleared them
- a one-time copy of a few settings: your first name for Aria, your morning-reminder time and onboarding status

Your phone number is **not** synced to iCloud.

This copy is stored by Apple, encrypted in transit and on Apple's servers, and is available only to devices signed in to your Apple Account. It never goes to us. Apple's handling of it is covered by [Apple's privacy policy](https://www.apple.com/legal/privacy/).

**To turn it off:** in the app, go to **Settings > iCloud Sync** and turn off **Sync with iCloud**. The app asks whether to also remove your data from iCloud. **Remove from iCloud** clears this device's tasks, schedules, streaks and settings copy from iCloud, and your other devices drop them too when they next sync; **Keep in iCloud** leaves the copy there. Either way nothing on this device is deleted, and nothing is synced until you turn the switch back on. Signing out of iCloud on the device (iOS **Settings**, tap your name, then **Sign Out**) also stops sync. If you are not signed in to iCloud, nothing is synced. See also [Your Choices](#your-choices-and-how-to-delete-your-data).

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
3. You verify your phone number. The app sends your number and your subscription receipt to our server; our server texts you a six-digit code through Twilio; you enter it in the app. Codes expire after 10 minutes and are held only as a hash in our server's memory. Aria can only ever call the number you verified. Aria currently calls numbers in the **US (+1) format** only.

### What is sent, each time a call is placed or scheduled

- Your **phone number**, so Aria can call you.
- Your **first name**, if you entered one in Settings.
- Your **incomplete tasks** (up to 50 of them, the most urgent first): their titles, priorities, due dates, scheduled dates and times, and internal task IDs. The Daily Check-in call carries the same list without due dates. Task **notes** and **completed tasks** are not sent.
- Your **time zone** and the **current time**, so Aria knows when "today" and "tomorrow" are.
- **Proof of your subscription**: the signed App Store receipt, so our server knows the call is paid for.

This applies to calls you start ("Call Me Now"), calls you schedule for a task, and the **Daily Check-in** call the app places for you at the time you set.

The app also sends the receipt, and nothing else, when it starts after you have agreed (and again when you agree, or when our server refuses a number), to ask our server which number is verified.

### Who receives it and why

- **Our Aria server**, hosted on Railway. It checks your subscription with Apple, confirms your number is verified, places the call, holds your task list from the moment you place or schedule a call until the call ends, and records the changes Aria makes so your phone can pick them up. See [Railway's privacy policy](https://railway.com/legal/privacy) for the hosting side.
- **Twilio**, which places the phone call to your number and sends you texts (the verification code, and a text if we have to end a call early because your daily allowance is used up). See [Twilio's privacy policy](https://www.twilio.com/en-us/legal/privacy).
- **OpenAI**, whose Realtime voice model is the voice you talk to. During the call our server gives it your first name, your task list, your time zone and the current time, and streams the conversation audio both ways. See [OpenAI's privacy policy](https://openai.com/policies/privacy-policy/) and its [API data-usage terms](https://developers.openai.com/api/docs/guides/your-data).
- **Apple**, which handles the subscription payment. We never see your payment details, only the signed receipt.

Railway, Twilio and OpenAI process this data only to provide their service to us (hosting, the call and texts, the voice), under terms that require them to protect it and forbid using it for their own purposes such as advertising. OpenAI's API terms state that data sent through its API is not used to train OpenAI's models and that abuse-monitoring logs are kept for up to 30 days.

We do not use your data to train AI models, we do not use it for advertising, and we never sell it.

### What comes back

Changes Aria makes on the call, such as marking a task done, changing a priority, scheduling a task or adding a new one, are sent back to your phone and applied to your tasks there, like any edit you make yourself.

### What our server keeps, and for how long

| Data | Kept for |
|---|---|
| The call set-up data for a call you start now, or the Daily Check-in (your name, task list, number, time zone) | Deleted 90 seconds after it is created if the call never connects, and when the call ends. The list of changes Aria made is kept for about one hour so your phone can fetch it, then deleted. |
| A scheduled call | Your number, your Aria subscription ID, the task's title and ID and the call time are saved to disk. Your first name, your task list and your time zone, as they were when you scheduled it, are held in memory until the call fires (they do not survive a server restart). Kept until the call fires or you cancel it; a call more than 24 hours overdue is dropped. If our server restarts before the call, only those are kept, and Aria calls with just the number, the task title and ID and the time. |
| Your verified phone number, paired with your Aria subscription ID (Apple's transaction identifier; not your name or Apple ID) | Until you verify a different number, or ask us to delete it. This includes after your subscription ends. |
| Calls per subscription (a count only) | Reset each day. |
| Calls per phone number | The number and the times of its calls in the last 24 hours. Once a number has placed no call for 24 hours, its entry is dropped the next time the counters are saved or our server starts. |
| The result of checking your subscription with Apple | 10 minutes. |
| Call audio | Streams through our server and is never recorded or written to disk by us. OpenAI receives the audio to run the voice; its API terms allow it to keep abuse-monitoring logs for up to 30 days and say the audio is not used for training. |
| Server logs | Log lines record that a call or verification happened, with only the last four digits of your number; error messages from our phone provider are redacted the same way before they are logged, and are not passed on to the app. Tokens are redacted. Task titles are kept out of logs, including the titles of tasks Aria adds or changes during a call, unless a debugging setting is turned on, which is off by default. Like any web request, requests to our server also carry your device's internet address, which our hosting provider logs. Railway keeps server logs for 7 days. |

**We do not keep:** call audio (it streams through our server and is never recorded or written to disk), transcripts, verification codes, or the receipt itself. OpenAI's handling of the audio is described in the table above.

While a call is in progress, we can see that a call is active, the number and its duration, and we can end it. We have no way to listen in.

### How we protect it

- Everything the app sends travels encrypted: HTTPS to our server, and encrypted connections from our server to Twilio and OpenAI.
- Verification codes are stored only as a one-way hash in our server's memory, never on disk or in logs.
- Access to our server's admin functions is limited to the developer and protected by a secret.

### Records from a retired promotion

An earlier version of the app offered a free month in return for a review and asked for a name, an email address and the review text for that. The offer has been withdrawn. The version of the app in the App Store today (3.7) still shows the form; the next version removes it. Since September 27, 2026 our server refuses every submission from that form (it answers with an error and stores nothing), and the server in place before it did not keep the submissions it received, so no records from that form exist. If you sent one and would like confirmation, email us.

## Purchases

Full Access, Remi and Aria are bought through Apple. Apple handles payment and we never see your card or Apple ID. For Aria only, the app sends the signed App Store receipt to our server to confirm the subscription is active. Nothing about Full Access or Remi purchases leaves your device.

## Notifications

Reminders and Aria call alerts are local notifications generated on your device. We run no push-notification server and no notification data is sent to us.

## What We Do Not Do

- No analytics or usage tracking.
- No advertising and no advertising identifiers.
- No third-party SDKs in the app. Apart from Apple's own services (iCloud, the App Store), the only server the app talks to is our Aria server. When you open the Aria screen (or Settings, once you are subscribed), the app asks that server whether Aria is available; that request carries nothing about you beyond what any web request includes (your device's internet address, which our hosting provider may keep briefly in its connection logs).
- No selling, renting or trading of your data.
- No AI training on your data.
- No accounts. What our server can tie together is your phone number, your first name if you gave one, your task titles for the length of a call (or from scheduling until the call, for a scheduled call), and your Aria subscription's transaction ID. It never has your Apple ID, email or payment details.

## Your Choices and How to Delete Your Data

**On your device**
- Delete any task from its detail screen.
- **Settings > Clear All Data** erases your tasks, schedules and streaks on this device, and removes your tasks, schedules, streak history and the one-time copy of your settings (including your first name) from iCloud. Your other devices delete the same tasks, schedules and streaks when they next sync. A device still running an older version of the app cannot do this for schedules and streaks and may put its own copy back, so update the app there first, or run **Clear All Data** on that device too. This reaches iCloud only while **Sync with iCloud** is on. If you turned it off and chose **Keep in iCloud**, turn it back on first, then run **Clear All Data** or turn it off again and choose **Remove from iCloud**.
- Uninstalling the app removes everything stored on the device, including your data-sharing choice for Aria, except the two Keychain items described above, which iOS keeps across a reinstall. The iCloud copy stays in your iCloud account, so use **Clear All Data** first if you want it gone from there too.

**iCloud**
- Turn off **Settings > iCloud Sync > Sync with iCloud** in the app, and choose whether to remove your data from iCloud, as described under [iCloud Sync](#icloud-sync-your-own-account). Signing out of iCloud on the device also stops sync.

**Calendar**
- Revoke access in iOS **Settings > Privacy & Security > Calendars**.

**Aria**
- To withdraw the agreement you gave on the **How Aria Uses Your Data** screen, remove your phone number in **Settings > Aria**. The app then cancels every call you had scheduled on our server (this needs an internet connection at that moment), the call alerts on your phone and the **Daily Check-in**, places no new calls and sends none of your tasks or your name. While you stay subscribed, it still sends only the receipt when it starts, to ask which number is verified.
- To stop particular calls while keeping your number, turn Aria off on those tasks (which cancels them on our server) or turn off **Daily Check-in**.
- Deleting a task, or **Clear All Data**, does not cancel a call already scheduled for that task on our server; turn Aria off on the task first (which cancels it), or remove your number.
- Cancel the subscription in your Apple subscription settings. After it ends, our server refuses every request that carries your data (a call, a scheduled call or a number check) and keeps nothing from it beyond the usual log line; cancelling a scheduled call still works. If you left **Daily Check-in** or a task's Aria call switched on, the app may still attempt that call, and the attempt carries the same data as any call; a call already scheduled on our server also still fires unless you cancel it. Turn those off, or remove your number, and the app sends nothing more.
- Your verified number stays on our server, including after your subscription ends, until you verify a different number or email us to delete it (see below).

**On our server**
- Email **alonsobardales.apps@gmail.com** from any address and include the phone number you verified. We will reply within 48 hours and delete your verified number, any scheduled calls and any entry for your number still in our call counters within 30 days; in practice it is usually done within a few days. Data for a call you started is deleted automatically within about an hour of the call, and the counter entry for your number about a day after its last call, so there is nothing else to remove.

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

## If Your Version Does Not Have Aria Yet

We switch Aria on from our server, so the version of the app you have may not offer it yet. If it does not, the Aria section does not apply to you yet; your tasks stay on your device and in your own iCloud account exactly as described above. The only thing an older version can send us is the review form described under [Records from a retired promotion](#records-from-a-retired-promotion), if your version still has it (the App Store version 3.7 does); our server refuses those submissions and keeps nothing from them.

---

| | |
|---|---|
| **Data Storage** | On your device, plus your own iCloud account if you are signed in |
| **Data Sent to Us** | None of your data, unless you subscribe to Aria and agree in the app |
| **What Aria Sends** | Phone number, first name, incomplete task titles/priorities/dates, time zone, App Store receipt |
| **Who Receives It** | Our server on Railway, Twilio (call and texts), OpenAI (voice) |
| **Kept on Our Server** | Verified number paired with subscription ID until you change it or ask, including after your subscription ends; data for a call you start for about an hour after it; a scheduled call, with your name and task list as of scheduling, until it fires or you cancel it; the times of your last day's calls, dropped about a day after your last call |
| **Call Audio** | Streams through; never recorded by us. OpenAI may keep abuse-monitoring logs for up to 30 days |
| **Third-Party SDKs in the App** | None |
| **Analytics** | None |
| **Advertising** | None |
| **Tracking** | None |
| **AI Training on Your Data** | Never by us; OpenAI's API terms say the same for data sent through the API |
| **Data Sold** | Never |
| **Delete Your Data** | Settings > Clear All Data; remove your number in Settings > Aria (which cancels scheduled calls); uninstall; email us for server-side deletion |
