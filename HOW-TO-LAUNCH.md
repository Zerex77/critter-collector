# Launching Critter Collector from a Windows PC

You don't need a Mac. Codemagic builds the iPhone app on a cloud Mac every time the code on GitHub changes, then uploads it to Apple. Everything below happens in a web browser, plus the TestFlight app on your iPhone.

Plan on about two hours of clicking spread over a few days, mostly waiting on Apple.

| Step | Where | Time | Cost |
|---|---|---|---|
| 1. Join the Apple Developer Program | Apple Developer app or developer.apple.com | 15 min, then 1–2 days for approval | $99/year |
| 2. Put the project on GitHub | github.com | 5 min | Free |
| 3. Register the app with Apple | developer.apple.com, appstoreconnect.apple.com | 20 min | Free |
| 4. Connect Codemagic and build | codemagic.io | 20 min, plus ~20 min per build | Free tier |
| 5. Test on your iPhone | TestFlight app | 10 min | Free |
| 6. Create the in-app purchases | App Store Connect | 30 min | Free |
| 7. Fill in the listing and submit | App Store Connect | 30 min, then 1–3 days of review | Free |

---

## 1. Join the Apple Developer Program

The easiest way is the **Apple Developer** app on your iPhone: open it, tap **Account**, sign in with your Apple ID and tap **Enroll Now**. You can also enroll at https://developer.apple.com/programs/enroll/.

Enroll as an **Individual**. Your name shows on the App Store as the seller. Approval usually takes a day or two. You can do step 2 while you wait.

## 2. Put the project on GitHub

1. On github.com, click **New repository**.
2. Name it `critter-collector`, choose **Public**, and leave every other box unchecked.
3. Upload this whole folder. If Claude is helping you, just say the repo is ready and Claude can upload it for you.
4. In the repo, go to **Settings → Pages**. Under **Branch** choose `main` and `/ (root)`, then **Save**.

After a minute you'll have these links:

- Game: `https://YOUR-USERNAME.github.io/critter-collector/` (a playable, shareable copy of the game that doesn't need a Claude account)
- Privacy policy: `https://YOUR-USERNAME.github.io/critter-collector/privacy.html`
- Support page: `https://YOUR-USERNAME.github.io/critter-collector/support.html`

Before you submit, replace `[your support email]` in `privacy.html` and `support.html` with a real address. Apple requires a working contact.

## 3. Register the app with Apple

Do this once your developer account is approved.

**Create the App ID**
1. Go to https://developer.apple.com/account/resources/identifiers/list and click **+**.
2. Choose **App IDs → App**, then **Continue**.
3. Description: `Critter Collector`. Bundle ID: **Explicit**, `com.zacharyelam.crittercollector`.
4. In-App Purchase is already checked. Click **Continue**, then **Register**.

**Create the app listing**
1. Go to https://appstoreconnect.apple.com → **Apps** → **+** → **New App**.
2. Platform **iOS**, name **Critter Collector**, language **English (U.S.)**, bundle ID **com.zacharyelam.crittercollector**, SKU `crittercollector`.
3. If the name is taken, try `Critter Collector: Zoo Merge`. Then change `appName` in `capacitor.config.json` and `<title>` in `www/index.html` to match.

**Turn on payments**
Go to **Business** (Agreements, Tax, and Banking). Accept the **Paid Apps** agreement and fill in bank and tax info. In-app purchases don't work until this shows **Active**.

**Make an API key for Codemagic**
1. Go to **Users and Access → Integrations → App Store Connect API**.
2. Click **Generate API Key**. Name `Codemagic`, access **App Manager**.
3. Download the `.p8` file right away. Apple only lets you download it once.
4. Write down the **Issuer ID** (above the key list) and the **Key ID**.

## 4. Connect Codemagic and build

1. Go to https://codemagic.io and **Sign up with GitHub**.
2. Click **Add application**, pick the `critter-collector` repo, and choose **Ionic / Capacitor** if it asks for a project type.
3. Open **Team settings → Team integrations → Developer Portal** and click **Connect**:
   - **App Store Connect API key name:** `Critter Collector` (it must match `codemagic.yaml` exactly)
   - **Issuer ID** and **Key ID:** from step 3
   - **API key:** upload the `.p8` file
4. Open **Team settings → Code signing identities → iOS certificates** and click **Generate certificate**. Choose **Apple Distribution**, give it any name and save it. Codemagic creates and stores the signing certificate, so you never need a Mac keychain.
5. Go back to the app, click **Start new build**, choose the **ios-release** workflow and the `main` branch.

The build takes about 15–25 minutes. When it's green, the app is uploaded to App Store Connect. Apple then processes it for another 10–30 minutes before it shows up in TestFlight.

After this, every change pushed to `main` makes a new build automatically.

If a build fails, download the build log and send it to Claude.

## 5. Test on your iPhone

1. In App Store Connect, open your app → **TestFlight**.
2. Under **Internal Testing**, click **+**, make a group and add yourself.
3. Install **TestFlight** from the App Store on your iPhone and accept the invite.

Play for a while. Check that sounds, haptics, saving and offline earnings work.

To test purchases without being charged, make a sandbox tester in **Users and Access → Sandbox** and sign in with it when the purchase sheet appears.

## 6. Create the in-app purchases

In App Store Connect, open your app → **Monetization → In-App Purchases** and create these five. The Product IDs must match exactly.

| Product ID | Type | Price | Reference name | Display name | Description |
|---|---|---|---|---|---|
| `critters.starter` | Non-Consumable | $1.99 | Starter Zoo | Starter Zoo | 250 gems and two level-7 animals |
| `critters.vippass` | Non-Consumable | $6.99 | VIP Pass | VIP Pass | Permanent 2× income and 8h offline earnings |
| `critters.gems.small` | Consumable | $0.99 | Gems 80 | Handful of Gems | 80 gems |
| `critters.gems.medium` | Consumable | $4.99 | Gems 500 | Bag of Gems | 500 gems |
| `critters.gems.large` | Consumable | $9.99 | Gems 1200 | Treasure Vault | 1,200 gems |

Each one needs a **review screenshot**. A screenshot of the Shop screen works for all five. Take it from TestFlight on your phone.

## 7. Fill in the listing and submit

Open your app → the **1.0 Prepare for Submission** page.

- **Screenshots:** upload the five images in `appstore/screenshots/` to the **iPhone 6.9" Display** slot.
- **Promotional text, description, keywords:** copy from `README.md` under "Starter store listing".
- **Support URL:** your `support.html` link. **Marketing URL:** optional, can be your game link.
- **Build:** pick the build Codemagic uploaded.
- **In-App Purchases:** add all five.
- **App Review Information:** add your phone and email. Write "No login required" in the notes.

Then, in the left menu:

- **App Privacy:** add your privacy policy link and choose **Data Not Collected**.
- **Age Rating:** answer "None" to everything. It should come out 4+.
- **Pricing and Availability:** **Free**, all countries.

Click **Add for Review**, then **Submit**. Review usually takes 1–3 days.

### If Apple rejects it

- **Guideline 4.2 (Minimum Functionality):** Apple sometimes questions apps built with web technology. Reply that the whole game is bundled in the app, works fully offline, and uses native haptics and in-app purchases.
- **In-app purchase problems:** usually the Paid Apps agreement isn't active, or a purchase is missing its review screenshot.
- **Broken links:** make sure the privacy and support pages load and show a real email address.

Send Claude the rejection message and it can help you write the reply or fix the issue.
