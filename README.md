

# ICP Ad Network API

![License](https://img.shields.io/github/license/dickhery/ad-network-api)
![Version](https://img.shields.io/github/package-json/v/dickhery/ad-network-api)

## Overview

The **ICP Ad Network API** provides a simple way for web developers to **integrate advertisements** into their Internet Computer (IC) projects and **earn ICP tokens** from genuine ad views. By leveraging a lightweight, **ephemeral token** mechanism, it ensures only ads that remain visible for at least five seconds count as “valid” views. This helps reduce fraudulent views and ensures better reliability for advertisers and creators alike.

## Table of Contents
- [Features](#features)
- [How It Works](#how-it-works)
  - [The Ad Network Canister](#the-ad-network-canister)
  - [Ephemeral Tokens and the 5-Second Timer](#ephemeral-tokens-and-the-5-second-timer)
- [Getting Started](#getting-started)
  - [Installation](#installation)
  - [Including the Bundle in Your HTML Project](#including-the-bundle-in-your-html-project)
- [Step-by-Step Integration Guide](#step-by-step-integration-guide)
  1. [Initialize the Ad Network Actor](#1-initialize-the-ad-network-actor)
  2. [Fetch an Ad (Plus Ephemeral Token)](#2-fetch-an-ad-plus-ephemeral-token)
  3. [Display the Ad and Wait 5 Seconds](#3-display-the-ad-and-wait-5-seconds)
  4. [Call recordViewWithToken to Count the View](#4-call-recordviewwithtoken-to-count-the-view)
  5. [Handle Cancelation or Layout Changes](#5-handle-cancelation-or-layout-changes)
- [Minimal Example (ad-network-api.html)](#minimal-example-ad-network-apihtml)
- [Available Ad Types](#available-ad-types)
- [Earning ICP Tokens](#earning-icp-tokens)
- [Technical Notes](#technical-notes)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

---

## Features

- **Lightweight & Easy Integration**  
  Quickly embed the ad network code into an existing front-end to start monetizing.

- **Ephemeral Token Security**  
  Each fetched ad comes with a short-lived token that must be returned only after 5 seconds to confirm a “genuine view.”

- **ICP Token Rewards**  
  Earn ICP tokens automatically based on your ad impressions, which can be tracked within your canister or user account.

- **Multiple Ad Types**  
  You can specify different ad shapes, such as “Horizontal Banner Portrait” or “Full Page,” so the returned ad best fits your project layout.

- **Compatibility**  
  Works in all modern browsers, with minimal overhead.

---

## How It Works

### The Ad Network Canister

The **Ad Network Canister** is a smart contract on the Internet Computer that:

1. **Manages Advertisers & Ads**: Stores ad images, links, and view counts.  
2. **Distributes Ephemeral Tokens**: On each `getNextAd` call, it returns `(Ad, tokenId)`, where `tokenId` is short-lived.  
3. **Rewards 5-Second Views**: Once you call `recordViewWithToken(tokenId)` after 5 seconds, the canister updates the view count. You can convert any views your project has generated into ICP at any time.

### Ephemeral Tokens and the 5-Second Timer

When you fetch an ad, the canister also sends you a **token** that represents that specific ad impression. By default, the ad network canister expects you to **hold** this token for at least 5 seconds while the ad is visible on screen. If the user navigates away before 5 seconds, you simply **do not** send the token back to `recordViewWithToken`, so the view does not count.

---

## Getting Started

### Installation

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/dickhery/ad-network-api.git
   ```
2. **Install Dependencies**:
   ```bash
   cd ad-network-api
   npm install
   ```
3. **Build the Bundle**:
   ```bash
   npm run build
   ```
   This process creates a `dist/ad-network-api.bundle.js` file containing all the essential calls (`initActorUnauthenticated`, `getNextAd`, `recordViewWithToken`, etc.).

### Including the Bundle in Your HTML Project

After building, **copy** `ad-network-api.bundle.js` into your project’s `js/` folder (or anywhere you like). Then add a `<script>` tag in your HTML:

```html
<script src="./js/ad-network-api.bundle.js"></script>
```

This exposes a global `AdNetworkAPI` object that you can call directly in your scripts.

---

## Step-by-Step Integration Guide

Below is a quick summary of how to integrate ads into an **HTML** front-end. For a thorough code example, see the [Minimal Example](#minimal-example-ad-network-apihtml).

### 1. Initialize the Ad Network Actor

Before fetching any ads, **initialize** the canister actor in anonymous mode:
```js
await AdNetworkAPI.initActorUnauthenticated();
```
This sets up your front-end to make calls to the canister read-only.

### 2. Fetch an Ad (Plus Ephemeral Token)

Use the canister call:
```js
const [adObject, tokenId] = await AdNetworkAPI.getNextAd("MyProjectID", "Horizontal Banner Portrait");
```
- **`MyProjectID`**: A string that identifies your project in the ad network.  
- **`Horizontal Banner Portrait`**: The type of ad layout you want.

If no ads are available, you get `null` or an empty result.

### 3. Display the Ad and Wait 5 Seconds

Update your HTML DOM:
```js
adImage.src = `data:image/png;base64,${adObject.imageBase64}`;
adLink.href = adObject.clickUrl;
```
Then **start a 5-second timer**. If the user **remains** on the page for 5 seconds, proceed to Step 4.

### 4. Call `recordViewWithToken` to Count the View

After 5 seconds:
```js
await AdNetworkAPI.recordViewWithToken(tokenId);
```
If successful, the canister increments the view count. If you call too soon, it returns `false` or an error, meaning the view didn’t count.

### 5. Handle Cancelation or Layout Changes

If the user navigates away **before** 5 seconds, **do not** call `recordViewWithToken`. The ephemeral token is effectively **canceled**, ensuring no partial or fraudulent views.

---

## Minimal Example ([ad-network-api.html](./ad-network-api.html))

Check out `ad-network-api.html` for a **complete** minimal demonstration:

- It shows exactly how to fetch an ad, display it, and only after 5 seconds call `recordViewWithToken(tokenId)`.
- If the user leaves early, the code cancels the timer and never finalizes the view.

---

## Available Ad Types

Below are **some** ad type strings you can pass to `getNextAd(projectId, adType)`:

1. **"Horizontal Banner Portrait"**  
2. **"Full Page"**  
3. **"Vertical Banner"**  
4. **"Square"**  

*(You can define any custom string if your canister code handles it. Future expansions may differentiate more shapes and sizes.)*

---

## Earning ICP Tokens

1. **Views Are Counted**: Each ephemeral token that’s returned via `recordViewWithToken(tokenId)` after 5 seconds increments your view count.  
2. **Automatic Rewards**: The canister updates your total. Depending on your usage, it can seamlessly distribute ICP tokens to the project owner or your advertiser.  
3. **No Manual Payout**: Typically, the ad network canister either credits your principal automatically or provides a method to “cash out” your project’s aggregated views.

*(Implementation details vary depending on the ad network’s canister logic—some require you to call a “withdraw” or “cashOutProject” function to receive tokens.)*

---

## Technical Notes

- **Anonymous vs. Authenticated**: You can keep it simple with `initActorUnauthenticated()`. If you need user-level control or the user is also an **advertiser** posting ads, you can integrate a wallet or an Internet Identity.  
- **Time Enforcement**: The canister checks the time difference between `getNextAd` and `recordViewWithToken` internally. A typical default is 5 seconds, but your canister code might set a different threshold.  
- **Front-End**: The front-end must hold the ephemeral token. If the user leaves or refreshes, you lose the token (no double counting or partial counting).  

---

## Contributing

We’re happy to accept community contributions—feel free to open issues or pull requests if you have improvements, bug fixes, or new ad type ideas!

---

## License

This project is licensed under the [MIT License](LICENSE). Please see the file in this repository for full details.

---

## Contact

For questions, suggestions, or support, reach out at:

- **Email:** dickhery@gmail.com  
- **GitHub:** [dickhery](https://github.com/dickhery)

Thank you for choosing the **ICP Ad Network API**! We can’t wait to see how you **monetize your IC projects** with embedded ads. Remember to check out [`ad-network-api.html`](./ad-network-api.html) or `index.html` for full working demos.