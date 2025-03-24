# Bypassing CAPTCHAs with Playwright

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/)

This guide explains how to bypass CAPTCHAs using Playwright and ensure your web scraping tasks run smoothly without interruptions:

- [What Are CAPTCHAs and Can You Bypass Them?](#what-are-captchas-and-can-you-bypass-them)
- [Playwright Bypass CAPTCHA: Step-By-Step Tutorial](#playwright-bypass-captcha-step-by-step-tutorial)
  - [Step #1: Initialize Your Node.js Project](#step-1-initialize-your-nodejs-project)
  - [Step #2: Install Playwright Extra](#step-2-install-playwright-extra)
  - [Step #3: Set Up Your Playwright Script](#step-3-set-up-your-playwright-script)
  - [Step #4: Implement the Browser Automation Logic](#step-4-implement-the-browser-automation-logic)
  - [Step #5: Install the Playwright Stealth Plugin](#step-5-install-the-playwright-stealth-plugin)
  - [Step #6: Register the Stealth Settings](#step-6-register-the-stealth-settings)
  - [Step #7: Repeat the Bot Detection Test](#step-7-repeat-the-bot-detection-test)
- [What If the Playwright CAPTCHA Solver Solution Does Not Work?](#what-if-the-playwright-captcha-solver-solution-does-not-work)
 
## What Are CAPTCHAs and Can You Bypass Them?

A CAPTCHA, short for “Completely Automated Public Turing tests to tell Computers and Humans Apart,” is a test used to distinguish between human users and automated bots. Humans can typically solve them easily, but machines are supposed to find them challenging.

![Example of a CAPTCHA](https://brightdata.com/wp-content/uploads/2024/07/Example-of-a-CAPTCHA.png)

Google reCAPTCHA, hCaptcha, and BotDetect are some of the most popular CAPTCHA providers. These usually support one or more of the CAPTCHA types below:

- **Image-based challenges:** Users must identify specific objects in a grid of images.
- **Text-based challenges:** Users are required to type a sequence of distorted letters and numbers.
- **Audio-based challenges:** Users are asked to type the words they hear.
- **Puzzle challenges:** Users must solve a simple puzzle, such as sliding a piece into place.

CAPTCHAs can be part of a particular user flow, such as the final step of submitting a form:

![CAPTCHA as a step of a form submission process example](https://brightdata.com/wp-content/uploads/2024/07/CAPTCHA-as-a-step-of-a-form-submission-process-example.png)

In these cases, the CAPTCHA is always displayed and cannot be avoided by bots. However, you can integrate your software with CAPTCHA-solving libraries to automate them or services that rely on human operators to solve these challenges in real-time.

CAPTCHAs are also commonly used as part of broader anti-bot solutions, such as [web application firewalls](https://www.cloudflare.com/learning/ddos/glossary/web-application-firewall-waf/):

![Example of a Web Application Firewall](https://brightdata.com/wp-content/uploads/2024/07/Example-of-a-Web-Application-Firewall-1024x488.png)

These systems dynamically display a CAPTCHA when they suspect the user may be a bot. In these instances, CAPTCHAs can be bypassed by making your bot mimic human behavior.

## Playwright Bypass CAPTCHA: Step-By-Step Tutorial

An eficient approach to avoid CAPTCHAs is to simulate human behaviors in an automated script while using a human-like fingerprint. [Playwright](https://playwright.dev/) is a leading browser automation library and one of the best tools for this purpose is.

The tutorial section of this guide explains how to implement Playwright CAPTCHA bypassing logic.

### Step #1: Initialize Your Node.js Project

Skip this step if you already have a Playwright web scraping or testing script. Otherwise, create a folder for your Playwright CAPTCHA solver project and enter it in the terminal:

```bash
mkdir playwright_demo

cd playwright_demo
```

Initialize a new Node.js project inside it:

```bash
npm init -y
```

Open the project’s folder in your preferred JavaScript IDE and add a new `script.js` file.

![Adding a new script.js file in the IDE](https://brightdata.com/wp-content/uploads/2024/07/Adding-a-new-script.js-file-in-the-IDE.png)

Do not forget to open `package.json` and [mark your project as a module](https://nodejs.org/api/packages.html#type) by adding `"type": "module"`.

### Step #2: Install Playwright Extra

Playwright's lack of support for plugins has been made up by the community with [Playwright Extra](https://github.com/berstend/puppeteer-extra/tree/master/packages/playwright-extra#readme).

Add `playwright` and [`playwright-extra`](https://www.npmjs.com/package/playwright-extra) to your project’s dependencies:

```bash
npm i playwright playwright-extra
```

### Step #3: Set Up Your Playwright Script

It is time to initialize your script to let Playwright solve CAPTCHA challenges. To import the browser you want to control from `playwright-extra`, add this line to `script.js`:

```js
import { chromium } from "playwright-extra"
```

Initialize a new [async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) function where to perform the human-like interaction using the Playwright API:

```js
(async () => {

    // set up the browser and launch it

    const browser = await chromium.launch()

    // open a new blank page

    const page = await browser.newPage()

    // browser automation logic...

    // close the browser and release its resources

    await browser.close()

})()
```

This launches a new Chromium instance and opens a new page before closing the browser.

### Step #4: Implement the Browser Automation Logic

The target site will be [bot.sannysoft.com](https://bot.sannysoft.com/), a special web page that runs some tests in the browser to find out whether the user is a human or a bot. If you try to visit this page on your local browser, you should see that all the tests are passed.

Connect to the target page using the [`goto()`](https://playwright.dev/docs/api/class-page#page-goto) method:

```js
await page.goto("https://bot.sannysoft.com/")
```

Now, create a screenshot of the entire page to see the results of the anti-bot tests:

```js
await page.screenshot("results.png")
```

Put it all together, and you will get the following `script.js` file:

```js
import { chromium } from "playwright-extra"

(async () => {

    // set up the browser and launch it

    const browser = await chromium.launch()

    // open a new blank page

    const page = await browser.newPage()

    // navigate to the target page

    await page.goto("https://bot.sannysoft.com/")

    // take a screenshot of the entire page

    await page.screenshot({

        path: "results.png",

        fullPage: true

    })

    // close the browser and release its resources

    await browser.close()

})()
```

Execute the above code with the command below:

```bash
node script.js
```

The script will open a Chromium instance in headless mode, visit the desired page, take a screenshot, and then close the browser. If you open the `results.png` file that will appear in the project root folder at the end of script execution, you will see:

![results.png file example](https://brightdata.com/wp-content/uploads/2024/07/results.png-file-example-196x1024.png)

Vanilla Playwright in headless mode does not pass several tests. To fix it, use the Stealth plugin.

### Step #5: Install the Playwright Stealth Plugin

[Playwright Stealth](https://github.com/berstend/puppeteer-extra/tree/39248f1f5deeb21b1e7eb6ae07b8ef73f1231ab9/packages/puppeteer-extra-plugin-stealth) is a plugin for `playwright-extra` to prevent bot detection. It overrides several configurations to make the browser instance appear to be natural, as if it was not being controlled by Playwright.

The Stealth plugin was originally developed for Puppeteer Extra, but it also works for Playwright Extra. Install it with npm:

```bash
npm i puppeteer-extra-plugin-stealth
```

Next, import the Stealth plugin in your `script.js` file with this line:

```js
import StealthPlugin from "puppeteer-extra-plugin-stealth"
```

### Step #6: Register the Stealth Settings

To implement Playwright CAPCHA bypass logic, simply register the Stealth plugin in `playwright-extra` through the `use()` method:

```js
chromium.use(StealthPlugin())
```

The browser controlled by Playwright will now appear as a real-world browser in use by a human user.

### Step #7: Repeat the Bot Detection Test

Here is what your `script.js` file should currently look like:

```js
import { chromium } from "playwright-extra"

import StealthPlugin from "puppeteer-extra-plugin-stealth"

(async () => {

    // register the Stealth plugin

    chromium.use(StealthPlugin())

    // set up the browser and launch it

    const browser = await chromium.launch()

    // open a new blank page

    const page = await browser.newPage()

    // navigate to the target page

    await page.goto("https://bot.sannysoft.com/")

    // take a screenshot of the entire page

    await page.screenshot({

        path: "results.png",

        fullPage: true

    })

    // close the browser and release its resources

    await browser.close()

})()
```

Launch the script again:

```bash
node script.js
```

Open `results.png` another time, and you will now see that all bot-detection tests have been passed:

![results.png file second example - bot-detection tests passed](https://brightdata.com/wp-content/uploads/2024/07/results.png-file-second-example-bot-detection-tests-passed-229x1024.png)

## What If the Playwright CAPTCHA Solver Solution Does Not Work?

Browser settings are not the only aspect that anti-bot tools focus their attention on. IP reputation is another key factor, and a free library doesn't help with that.

For simple CAPTCHAs that require only a single click, you can use the [`puppeteer-extra-plugin-recaptcha`](https://github.com/berstend/puppeteer-extra/tree/39248f1f5deeb21b1e7eb6ae07b8ef73f1231ab9/packages/puppeteer-extra-plugin-recaptcha) plugin. However, when dealing with more complex tools like Cloudflare, you need something more powerful.

If you are looking for a real Playwright CAPTCHA solver, try Bright Data [web scraping solutions](https://brightdata.com/products/web-unlocker). These provide superior unlocking capabilities with a dedicated CAPTCHA-solving feature to automatically handle reCAPTCHA, hCaptcha, Cloudflare Turnstile, AWS WAF Captcha, and many others.

Register now and start your free trial today.