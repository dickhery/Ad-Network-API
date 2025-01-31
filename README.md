# ICP Ad Network API

![License](https://img.shields.io/github/license/dickhery/ad-network-api)
![Version](https://img.shields.io/github/package-json/v/dickhery/ad-network-api)

## Overview

Welcome to the **ICP Ad Network API**, a streamlined solution designed exclusively for creators to effortlessly integrate advertisements into their web projects and start monetizing their content. Leveraging the power of the Internet Computer Protocol (ICP), this lightweight API allows developers to earn ICRC-1 tokens by displaying ads and generating genuine views within their applications.

## Table of Contents

- [Features](#features)
- [Getting Started](#getting-started)
  - [Installation](#installation)
  - [Including the API Bundle](#including-the-api-bundle)
- [Usage](#usage)
  - [Initializing the API](#initializing-the-api)
  - [Fetching and Displaying Ads](#fetching-and-displaying-ads)
- [Available Ad Sizes](#available-ad-sizes)
- [Earning ICRC-1 Tokens](#earning-icrc-1-tokens)
- [Example Project](#example-project)
- [Contributing](#contributing)
- [License](#license)
- [Contact](#contact)

## Features

- **Lightweight Integration:** Minimal setup required to incorporate ads into your projects.
- **Automatic Token Rewards:** Earn ICRC-1 tokens effortlessly based on the number of genuine ad views generated.
- **Multiple Ad Sizes:** Select from a variety of ad sizes to perfectly fit your project's design and layout.
- **Optimized Bundle:** A compact API bundle ensures quick loading times and efficient performance.
- **Browser Compatibility:** Designed to work seamlessly across all modern web browsers.

## Getting Started

### Installation

Follow these steps to integrate the ICP Ad Network API into your project:

1. **Clone the Repository:**

   ```bash
   git clone https://github.com/dickhery/ad-network-api.git
   ```

2. **Navigate to the Project Directory:**

   ```bash
   cd ad-network-api
   ```

3. **Install Dependencies:**

   Ensure you have [Node.js](https://nodejs.org/) and npm installed. Then, install the necessary packages:

   ```bash
   npm install
   ```

4. **Build the API Bundle:**

   Generate the bundled JavaScript file using Rollup:

   ```bash
   npm run build
   ```

   This will create a `dist/` directory containing `ad-network-api.bundle.js`.

### Including the API Bundle

To integrate the API into your web project, include the pre-built bundle in your HTML file:

1. **Copy the Bundle:**

   Copy `ad-network-api.bundle.js` from the `dist/` directory to your project's JavaScript folder.

2. **Include in HTML:**

   Add the following `<script>` tag to your HTML file:

   ```html
   <script src="path-to-your-js-folder/ad-network-api.bundle.js"></script>
   ```

   Replace `path-to-your-js-folder/` with the actual path where you placed the bundle.

## Usage

### Initializing the API

Before fetching ads, initialize the API to set up the necessary actors for interacting with the Ad Network canister.

```javascript
document.addEventListener("DOMContentLoaded", async () => {
  const api = AdNetworkAPI; // Access the global API

  try {
    await api.initActorUnauthenticated();
    displayStatus("Ad Network initialized anonymously.");
  } catch (error) {
    console.error("Initialization Error:", error);
    displayError("Failed to initialize Ad Network.");
  }

  // ... rest of your code
});
```

### Fetching and Displaying Ads

Use the API to fetch ads based on your project's ID and desired ad type.

```javascript
async function fetchAndDisplayAd() {
  try {
    const projectId = "YourProjectID"; // Replace with your actual Project ID
    const adType = "Horizontal Banner Portrait"; // Choose the desired ad type

    displayStatus("Fetching ad...");
    const adOpt = await api.getNextAd(projectId, adType);
    if (!adOpt || adOpt.length === 0) {
      displayError("No available ads.");
      return;
    }

    const ad = adOpt[0];
    if (!ad) {
      displayError("Failed to retrieve ad.");
      return;
    }

    // Update the DOM with the ad details
    const adContainer = document.getElementById("ad-container");
    const adImage = document.getElementById("ad-image");
    const adLink = document.getElementById("ad-link");

    adImage.src = `data:image/png;base64,${ad.imageBase64}`;
    adLink.href = ad.clickUrl;

    adContainer.style.display = "block";
    displayStatus(`Fetched Ad ID: ${ad.id}`);
  } catch (error) {
    console.error("Fetch Ad Error:", error);
    displayError("Error fetching ad.");
  }
}

// Attach event listener to the fetch ad button
document.getElementById("fetch-ad-button").addEventListener("click", fetchAndDisplayAd);
```

## Available Ad Sizes

Our Ad Network offers a variety of ad sizes to suit different project layouts and designs. To locate and utilize available ad sizes:

1. **Access Ad Types:**

   The API supports multiple ad types such as:

   - **Horizontal Banner Portrait**
   - **Vertical Sidebar Landscape**
   - **Square Thumbnail**
   - **Full-Width Leaderboard**

2. **Implementing Ad Sizes:**

   When fetching an ad, specify the desired ad type to ensure the ad fits seamlessly within your project's layout.

   ```javascript
   const adType = "Horizontal Banner Portrait"; // Choose from available ad types
   ```

## Earning ICRC-1 Tokens

Creators earn ICRC-1 tokens based on the genuine views their projects generate by displaying ads through the ICP Ad Network API. Here's how it works:

1. **Integrate Ads:**

   Incorporate ads into your project using the API as demonstrated in the [Usage](#usage) section.

2. **Generate Views:**

   Each time an ad is displayed and viewed by users, it counts as a genuine view.

3. **Receive Tokens:**

   The Ad Network automatically tracks the number of views and rewards you with ICRC-1 tokens proportional to the views generated.

4. **Manage Earnings:**

   Tokens are managed automatically, allowing you to focus on creating and growing your project without handling the token transactions manually.

## Example Project

To help you get started, we've provided an example project that demonstrates how to integrate the ICP Ad Network API into a simple HTML webpage.

### Features of the Example Project

- **Fetch and Display Ads:** Click a button to fetch an ad and display it on the webpage.
- **Responsive Design:** Ensures ads are displayed correctly across various devices.

### Running the Example Project Locally

1. **Navigate to the Example Directory:**

   ```bash
   cd ad-network-example
   ```

2. **Serve the Project:**

   If you have `serve` installed, run:

   ```bash
   serve .
   ```

3. **Open in Browser:**

   Navigate to the URL provided by `serve` (e.g., `http://localhost:5000`) to view and interact with the example project.

## Contributing

We welcome contributions from the community! If you'd like to contribute to the ICP Ad Network API, please follow these steps:

1. **Fork the Repository:**

   Click the "Fork" button at the top-right corner of this page.

2. **Clone Your Fork:**

   ```bash
   git clone https://github.com/dickhery/ad-network-api.git
   ```

3. **Create a New Branch:**

   ```bash
   git checkout -b feature/YourFeatureName
   ```

4. **Make Your Changes:**

   Implement your feature or bug fix.

5. **Commit Your Changes:**

   ```bash
   git commit -m "Add feature: YourFeatureName"
   ```

6. **Push to Your Fork:**

   ```bash
   git push origin feature/YourFeatureName
   ```

7. **Create a Pull Request:**

   Navigate to the original repository and create a pull request from your fork.

## License

This project is licensed under the [MIT License](LICENSE).

## Contact

For any questions, issues, or suggestions, feel free to reach out:

- **Email:** your.email@example.com
- **GitHub:** [dickhery](https://github.com/dickhery)

---

Thank you for choosing the **ICP Ad Network API**! We look forward to seeing the amazing projects you'll create and help monetize.