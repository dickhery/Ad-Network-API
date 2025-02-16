# ICP Ad Network API

![License](https://img.shields.io/github/license/dickhery/ad-network-api)
![Version](https://img.shields.io/github/package-json/v/dickhery/ad-network-api)

## Overview

Welcome to the **ICP Ad Network API**, a streamlined solution designed for creators who want to monetize their web projects using ICRC-1 tokens from the Internet Computer Protocol (ICP). This lightweight API allows developers to earn tokens by displaying ads and generating *genuine* views, enforced via a simple ephemeral token mechanism.

## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
  - [Installation](#installation)
  - [Including the API Bundle](#including-the-api-bundle)
- [Usage](#usage)
  - [Minimal Example (ad-network-api.html)](#minimal-example-ad-network-apihtml)
  - [Ephemeral Token Logic](#ephemeral-token-logic)
  - [Fetching and Displaying Ads](#fetching-and-displaying-ads)
- [Available Ad Sizes](#available-ad-sizes)
- [Earning ICRC-1 Tokens](#earning-icrc-1-tokens)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## Features

- **Lightweight Integration:** Minimal setup required to incorporate ads into your projects.
- **Automatic Token Rewards:** Earn ICRC-1 tokens based on genuine ad views.
- **Ephemeral Token Verification:** Ensures each ad remains visible for 5 seconds before counting a view.
- **Multiple Ad Sizes:** Select from a variety of ad sizes to fit your layout.
- **Optimized Bundle:** A compact API bundle ensures quick loading times.
- **Browser Compatibility:** Works across all modern browsers.

## Getting Started

### Installation

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/dickhery/ad-network-api.git
Navigate to the Project Directory:

bash
Copy
Edit
cd ad-network-api
Install Dependencies:

bash
Copy
Edit
npm install
Build the API Bundle:

bash
Copy
Edit
npm run build
This outputs a dist/ad-network-api.bundle.js (or your chosen custom location).

Including the API Bundle
Copy the generated .bundle.js file into your project's JS folder.

In your HTML, do:

html
Copy
Edit
<script src="path/to/ad-network-api.bundle.js"></script>
Usage
Minimal Example (ad-network-api.html)
We provide a super minimal demonstration in ad-network-api.html, which shows how to:

Initialize the Ad Network actor anonymously (initActorUnauthenticated).
Fetch an ad + ephemeral token (getNextAd).
Wait 5 seconds, then pass the ephemeral token back to recordViewWithToken to confirm the view.
Why 5 seconds?
This ensures the ad is actually viewed—the user or a script can’t just fetch and instantly claim a view. If they leave early, we do not call recordViewWithToken, so it’s canceled. This approach helps ensure more genuine ad impressions.

Ephemeral Token Logic
When calling getNextAd(projectId, adType), the Ad Network canister returns an ephemeral token (plus the ad content).

Hold that token in your front end for 5 seconds while the ad is displayed.
If the user remains, call recordViewWithToken(tokenId).
If the user navigates away earlier, do not call recordViewWithToken(tokenId), and the view is canceled.
Fetching and Displaying Ads
In your code:

js
Copy
Edit
await AdNetworkAPI.initActorUnauthenticated();
const [ad, tokenId] = await AdNetworkAPI.getNextAd("MyProject", "Horizontal Banner");
// display ad
setTimeout(() => {
  // 5 seconds later
  AdNetworkAPI.recordViewWithToken(tokenId);
}, 5000);
Available Ad Sizes
You can pass an adType string to getNextAd, e.g. "Horizontal Banner Portrait" or "Full Page". Future expansions or references might be added.

Earning ICRC-1 Tokens
Integrate Ads: Insert ads in your pages.
Generate Genuine Views: Each “ephemeral token” that’s returned to the canister after 5 seconds is credited as a valid view.
Automatically Rewarded: The canister tracks your total views, awarding you ICRC-1 tokens (depending on your usage or canister logic).
Manage Earnings: No manual transactions needed for each view—just track your ICRC-1 balance in your canister or user account.
Contributing
Please see CONTRIBUTING.md or open a PR if you want to help.

License
This project is licensed under the MIT License.

Contact
Feel free to reach out:

Email: your.email@example.com
GitHub: dickhery
Thank you for choosing the ICP Ad Network API. For a deeper example, see index.html, or for the barest approach, see ad-network-api.html.

php-template
Copy
Edit

**Note**: The link `[ad-network-api.html](./ad-network-api.html)` presumes that file is in the same repo, so that the README references it directly.

---

# 3) Final Versions of Updated Files

Below are the **two** files that require editing (or adding). The rest of your code can remain as is.

## A) `ad-network-api.html`

```html
<!-- ad-network-example/ad-network-api.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>ICP Ad Network API (Minimal)</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
    }
    #ad-container {
      margin-top: 20px;
      display: none;
    }
    #ad-image {
      max-width: 100%;
      height: auto;
    }
    #status-message {
      margin-top: 20px;
      color: green;
    }
    #error-message {
      margin-top: 20px;
      color: red;
    }
  </style>
</head>
<body>
  <h1>Minimal ICP Ad Network Integration</h1>

  <!-- 
    Button to fetch an ad from the canister.
    We'll display it for 5 seconds, then call recordViewWithToken.
  -->
  <button id="fetch-ad-button">Fetch & Display Ad</button>

  <div id="ad-container">
    <a id="ad-link" href="#" target="_blank">
      <img id="ad-image" src="" alt="Ad Image">
    </a>
  </div>

  <div id="status-message"></div>
  <div id="error-message"></div>

  <!-- 
    Minimal Ad Network API bundle 
    containing initActorUnauthenticated, getNextAd, recordViewWithToken
  -->
  <script src="./js/ad-network-api.bundle.js"></script>

  <script>
    (function() {
      const api = window.AdNetworkAPI;

      const fetchAdButton = document.getElementById("fetch-ad-button");
      const adContainer = document.getElementById("ad-container");
      const adLink = document.getElementById("ad-link");
      const adImage = document.getElementById("ad-image");
      const statusDiv = document.getElementById("status-message");
      const errorDiv = document.getElementById("error-message");

      let adViewTimeoutId = null;
      let currentTokenId = null;

      function displayStatus(msg) {
        statusDiv.textContent = msg;
        errorDiv.textContent = "";
      }
      function displayError(msg) {
        errorDiv.textContent = msg;
        statusDiv.textContent = "";
      }

      // Cancel the 5-second view timer if user navigates away or re-fetches
      function cancelAdTimeout() {
        if (adViewTimeoutId !== null) {
          clearTimeout(adViewTimeoutId);
          adViewTimeoutId = null;
          displayStatus("Ad view canceled before 5 seconds.");
        }
      }

      async function fetchAndDisplayAd() {
        try {
          cancelAdTimeout();

          displayStatus("Initializing & fetching ad...");
          adContainer.style.display = "none";
          adImage.src = "";
          adLink.href = "#";
          currentTokenId = null;

          // 1) init the canister actor
          await api.initActorUnauthenticated();

          // 2) getNextAd => returns [ad, tokenId]
          const projectId = "ExampleProjectMinimal";
          const adType = "Horizontal Banner Portrait";
          const result = await api.getNextAd(projectId, adType);

          if (!result || result.length === 0) {
            displayError("No ads found or empty result.");
            return;
          }
          const [ad, tokenId] = result;
          if (!ad) {
            displayError("No ad object returned from canister.");
            return;
          }

          adLink.href = ad.clickUrl;
          adImage.src = `data:image/png;base64,${ad.imageBase64}`;
          adContainer.style.display = "block";
          displayStatus(`Fetched Ad #${ad.id}, ephemeral token=${tokenId}`);

          currentTokenId = tokenId;

          // 3) Wait 5 seconds => recordViewWithToken(tokenId)
          adViewTimeoutId = setTimeout(async () => {
            if (!currentTokenId) return; // possibly canceled
            try {
              const success = await api.recordViewWithToken(currentTokenId);
              if (success) {
                displayStatus(`View for Ad #${ad.id} counted successfully!`);
              } else {
                displayError("View NOT counted (invalid or too soon).");
              }
            } catch (err) {
              console.error("recordViewWithToken error:", err);
              displayError("Error counting view: " + err.message);
            }
            adViewTimeoutId = null;
            currentTokenId = null;
          }, 5000);

        } catch (err) {
          console.error("fetchAndDisplayAd error:", err);
          displayError("Error fetching ad: " + err.message);
        }
      }

      fetchAdButton.addEventListener("click", fetchAndDisplayAd);

      displayStatus("Welcome! Click 'Fetch & Display Ad' to try.");
    })();
  </script>
</body>
</html>
B) Updated README.md
markdown
Copy
Edit
# ICP Ad Network API

![License](https://img.shields.io/github/license/dickhery/ad-network-api)
![Version](https://img.shields.io/github/package-json/v/dickhery/ad-network-api)

## Overview

Welcome to the **ICP Ad Network API**, a streamlined solution designed for creators who want to monetize their web projects using ICRC-1 tokens from the Internet Computer Protocol (ICP). This lightweight API allows developers to earn tokens by displaying ads and generating *genuine* views, enforced via a simple ephemeral token mechanism.

## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
  - [Installation](#installation)
  - [Including the API Bundle](#including-the-api-bundle)
- [Usage](#usage)
  - [Minimal Example (ad-network-api.html)](#minimal-example-ad-network-apihtml)
  - [Ephemeral Token Logic](#ephemeral-token-logic)
  - [Fetching and Displaying Ads](#fetching-and-displaying-ads)
- [Available Ad Sizes](#available-ad-sizes)
- [Earning ICRC-1 Tokens](#earning-icrc-1-tokens)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## Features

- **Lightweight Integration:** Minimal setup required to incorporate ads into your projects.
- **Automatic Token Rewards:** Earn ICRC-1 tokens based on genuine ad views.
- **Ephemeral Token Verification:** Ensures each ad remains visible for 5 seconds before counting a view.
- **Multiple Ad Sizes:** Select from a variety of ad sizes to fit your layout.
- **Optimized Bundle:** A compact API bundle ensures quick loading times.
- **Browser Compatibility:** Works across all modern browsers.

## Getting Started

### Installation

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/dickhery/ad-network-api.git
Navigate to the Project Directory:

bash
Copy
Edit
cd ad-network-api
Install Dependencies:

bash
Copy
Edit
npm install
Build the API Bundle:

bash
Copy
Edit
npm run build
This outputs a dist/ad-network-api.bundle.js (or your chosen custom location).

Including the API Bundle
Copy the generated .bundle.js file into your project's JS folder.

In your HTML, do:

html
Copy
Edit
<script src="path/to/ad-network-api.bundle.js"></script>
Usage
Minimal Example (ad-network-api.html)
We provide a super minimal demonstration in ad-network-api.html, which shows how to:

Initialize the Ad Network actor anonymously (initActorUnauthenticated).
Fetch an ad + ephemeral token (getNextAd).
Wait 5 seconds, then pass the ephemeral token back to recordViewWithToken(tokenId) to confirm the view.
Why 5 seconds?
This ensures the ad is actually viewed—the user or a script can’t just fetch and instantly claim a view. If they leave early, we do not call recordViewWithToken(tokenId), so it’s canceled. This approach helps ensure more genuine ad impressions.

Ephemeral Token Logic
When calling getNextAd(projectId, adType), the Ad Network canister returns an ephemeral token (plus the ad content).

Hold that token in your front end for 5 seconds while the ad is displayed.
If the user remains, call recordViewWithToken(tokenId).
If the user navigates away earlier, do not call recordViewWithToken(tokenId), and the view is canceled.
Fetching and Displaying Ads
In your code:

js
Copy
Edit
await AdNetworkAPI.initActorUnauthenticated();
const [ad, tokenId] = await AdNetworkAPI.getNextAd("MyProject", "Horizontal Banner");
// display ad
setTimeout(() => {
  // 5 seconds later
  AdNetworkAPI.recordViewWithToken(tokenId);
}, 5000);
Available Ad Sizes
You can pass an adType string to getNextAd, e.g. "Horizontal Banner Portrait" or "Full Page". Future expansions or references might be added.

Earning ICRC-1 Tokens
Integrate Ads: Insert ads in your pages.
Generate Genuine Views: Each “ephemeral token” that’s returned to the canister after 5 seconds is credited as a valid view.
Automatically Rewarded: The canister tracks your total views, awarding you ICRC-1 tokens (depending on your usage or canister logic).
Manage Earnings: No manual transactions needed for each view—just track your ICRC-1 balance in your canister or user account.
Contributing
We welcome community contributions! If you'd like to help, feel free to open a pull request or file an issue.

License
This project is licensed under the MIT License.

Contact
Feel free to reach out:

Email: your.email@example.com
GitHub: dickhery
Thank you for choosing the ICP Ad Network API!
Check out ad-network-api.html (minimal approach) or index.html for a more robust example.

markdown
Copy
Edit

That’s it! Now your project has:

- A **new** minimal HTML file named `ad-network-api.html`  
- A **revised** `README.md` guiding new users to integrate ephemeral tokens for 5-second ad views  

---

## 4) Conclusion

With these changes:

- **Users see** exactly how to incorporate ephemeral tokens and a 5-second wait.  
- The new `ad-network-api.html` is heavily commented, so new adopters can quickly understand the steps.  
- The updated README clarifies ephemeral token logic, direct references to your example file, and includes instructions about holding the token for five seconds before calling `recordViewWithToken`.  

Happy coding!