# Critter Collector — iOS build

**On Windows, or without a Mac? Follow `HOW-TO-LAUNCH.md` instead.** It builds the app in the cloud with Codemagic. The steps below are for building on your own Mac with Xcode.

Zoo merge-and-idle game. The whole game lives in `www/index.html`. Capacitor wraps it as a native iOS app, `cordova-plugin-purchase` handles Apple in-app purchases, and `@capacitor/haptics` gives the merge thumps.

Bundle ID is set to `com.zacharyelam.crittercollector` in `capacitor.config.json`. Change it if you want something else; it has to match what you register with Apple.

## What you need

- A Mac with Xcode (current version) and Node 20+
- An Apple Developer Program membership ($99/year) — https://developer.apple.com/programs/
- An iPhone for testing (recommended)

## 1. Build the Xcode project (about 10 minutes)

```bash
cd CritterCollector
npm install
npx cap add ios          # creates the ios/ Xcode project
npm run icons            # makes every icon and splash size from assets/
npx cap sync ios         # copies www/ and installs the plugins
npx cap open ios         # opens Xcode
```

In Xcode, select the **App** target:

- **Signing & Capabilities**: pick your team, then click **+ Capability** and add **In-App Purchase**.
- **General → Deployment Info**: set iPhone only and Portrait only.
- Press Run with your phone plugged in to play it natively.

Any time you edit `www/index.html`, run `npx cap sync ios` again.

## 2. Set up App Store Connect (about 30 minutes)

At https://appstoreconnect.apple.com:

1. **Agreements, Tax, and Banking**: accept the Paid Apps agreement and add bank and tax info. Purchases will not work until this is active.
2. **Apps → +**: create the app with the same bundle ID.
3. **Monetization → In-App Purchases**: create these five products. The IDs must match exactly.

| Product ID | Type | Price | Name |
|---|---|---|---|
| `critters.starter` | Non-Consumable | $1.99 | Starter Zoo |
| `critters.vippass` | Non-Consumable | $6.99 | VIP Pass |
| `critters.gems.small` | Consumable | $0.99 | Handful of Gems |
| `critters.gems.medium` | Consumable | $4.99 | Bag of Gems |
| `critters.gems.large` | Consumable | $9.99 | Treasure Vault |

Each needs a display name, description and a review screenshot (a screenshot of the Shop screen works). Prices shown in the game update automatically from the App Store once the products load.

4. **Users and Access → Sandbox**: add a sandbox tester email and buy things on your phone with it. Sandbox purchases are free.

## 3. Submit (about 30 minutes, then Apple's review)

In Xcode: **Product → Archive**, then **Distribute App → App Store Connect**. When the build appears in App Store Connect, fill in:

- **Screenshots**: 6.9" iPhone (1320 × 2868). Take them in the iPhone 16 Pro Max simulator with Cmd+S.
- **Description, keywords, support URL**: a starter description is below.
- **Privacy Policy URL**: host `privacy.html` anywhere public (GitHub Pages, your site) and paste the link.
- **App Privacy**: choose **Data Not Collected**. The game saves progress only on the device.
- **App name**: names on the App Store must be unique. If "Critter Collector" is taken, App Store Connect will tell you when you create the app; change `<title>` in `www/index.html` and `appName` in `capacitor.config.json` to match whatever you pick.
- **Age Rating**: answer the questionnaire. Nothing in the game should push it above 4+.
- Attach all five in-app purchases to this version, then **Submit for Review**.

Review usually takes 1–3 days. Common first-time rejections are missing purchase screenshots, an inactive Paid Apps agreement, or a privacy URL that doesn't load.

## Starter store listing

**Name:** Critter Collector
**Subtitle:** Merge animals, build an endless zoo
**Description:**
Adopt a chick, then drag two together to make something bigger. Chicks become bunnies, bunnies become ducks, and thirteen animals later you've raised a Champion Ox. Then open the next exhibit and do it again with wilder animals.

Start in the Petting Farm and work your way through the Woodland Trail, Bird Aviary, Reptile & Bug House, African Savanna, Rainforest, Polar Frontier, Aussie Outback, Prehistoric Park and Mythical Sanctuary, all the way to the Galactic Zoo. Every exhibit has a bigger grid and animals that earn 40× more.

Every animal earns coins, even while you're away. Build attractions from Balloon Carts to a Space Zoo Port, catch golden butterflies for huge boosts, and earn Zoo Stars that make your zoo stronger forever.

- Satisfying one-thumb drag-and-merge
- 11 themed exhibits with 143 animals, then endless rare variants
- 14 attractions to build, each doubling as you collect more
- Golden butterflies with zoo rushes, stampedes and lucky finds
- Zoo Stars and perks that automate everything, even opening new exhibits
- Endless achievements that boost your income
- Earns while you're offline
- No account needed, plays offline

**Keywords:** merge,idle,zoo,animals,tycoon,incremental,clicker,safari,prestige,offline

## Notes

- **Fonts** load from Google Fonts and fall back to the system rounded font offline. To bundle them, download Lilita One and Nunito into `www/fonts/` and swap the `<link>` for `@font-face` rules.
- **Saves** are in on-device storage. iOS can clear web storage under heavy storage pressure. If players invest real money, the next upgrade is `@capacitor/preferences` or iCloud sync.
- **Receipt validation** happens on device. That's fine for launch; add a server validator (the plugin supports one) if fraud becomes a problem.
- **Restore Purchases** is in the Shop, which Apple requires when you sell non-consumables.
- **Ads** aren't included. Rewarded ads (AdMob) would plug into the offline 2× and energy refill buttons, but they add App Tracking Transparency prompts and privacy labels.
