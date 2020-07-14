# Google Recaptcha v2 checkbox Flow Component for Salesforce

This component was developed by me, because I want to avoid any spam or brute force attacks on my flow, that is deployed with Lightning Out on an external page and on a community.

## Basic Concept
The flow component actually relies on three parts: An aura component, an HTML static resource and an Apex class. The aura component embeds the static resource as an iframe. The HTML file references the Google Recaptcha API and renders the Recaptcha. The iframe tells the aura component the current height and width of the content (e.g. recaptcha challenge) by using `Window.postMessage` and will also communicate the captcha response in the same way after the user completes the challenge. The aura component will take the token and call a method in the apex class, where the secret key is saved. The apex class will verify the token against the secret key in a callout to Google. If the verification is successful, the aura component will switch it's variable `isHuman` to `true` and let the user go to the next screen.

## Features

- Google Recaptcha v2 checkbox flow component
- A responsive layout, that adapts to the size of the recaptcha, especially the challenge window
- Server side verification of the recaptcha response
- Fully compatible to all browsers (tested with Firefox, Chrome, Internet Explorer, Chrome Mobile)

## Flow input and output variables

- `isHuman`: defaults to false, will be set to true, if the recaptcha verifies you as human
- `originPageURL`: insert the URL where the flow will run. e.g.: in the form of https://force-ability-5985-dev-ed--c.visualforce.com if you run it from the flow builder
- `enableServerSideVerification`: defaults to false, if set to true, the captcha response will be verified against your secret key in a callout to google
- `required`: defaults to true, make it required to pass the recaptcha
- `requiredMessage (optional)`: the error message displayed, if the user just clicks on next and has not verified he his human yet

## Installation instructions

At minimum for testing purposes, you just have to insert the correct `originPageURL` in the flow component. Then it will run with googles official test site and secret key. But in order to set up the component properly, you have to do the following:

### Part 1
Generate your own site and secret key here: https://www.google.com/recaptcha/

### Part 2
In the html file in the static resource `Google_Recaptcha`, update these lines and reupload the resource:
```javascript
var targetPageURL = "*";
var sitekey = '6Ldq2qwZAAAAAFtCcLEFEVkRk1V2EAe4FV1f4xnF';
```

`targetPageURL` will work with the wildcard `"*"`, but for security reasons, you should enter the URL, where the flow component runs. Remember: The component will only work if the domains in `targetPageURL` in the static resource, URL in your Browser and `originPageURL` in the flow components' input are the same (well except if you use the wildcard in the static resource, then only the last two need to be the same).

Setting the `targetPageURL` in the static resource to a specific URL means, that the flow component will only run on this domain. If you want to use the Recaptcha Component for example in your Org and in your community (with different domains), then leave the `targetPageURL` variable `"*"`.

Checkout this link to get more information: https://developer.mozilla.org/en-US/docs/Web/API/Window/postMessage

### Part 3
In the GoogleRecaptchaHandler apex class, insert your own secret key at the beginning:
```apex
private static String recaptchaSecretKey = '6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe';
```

## FAQ
- Why not use a lightning web component?
  - A lightning web component does not receive messages from the embedded iframe. Thus, the captcha response and the height of the content cannot be processed. This might be related to the Content Security Policy (CSP) of Salesforce.
  
- Why not use a Visualforce Page instead of a static resource?
  - A visualforce page is not public by default. Using Recaptcha means, that you often want to protect a flow, that is available for the public.

- Can you support Google Recaptcha v3?
  - I have to check but I don't think so. Maybe implementing Google Recaptcha v2 invisible would be fairly easy.

- The Recaptcha tells me, that it can only be used for testing purposes!
  - Create your own site and secret key and insert them as explained.

- Eventhough I successfully passed the recaptcha challenge, the flow will not let me go to the next screen.
  - Check the `originPageURL`variable and set it correctly. Otherwise the aura component will not receive the recaptcha response. Also check the `targetPageURL` in the static resource.

- Is this component secure?
  - Well, I hope so. As long as the input variables of an aura component are considered secure (are they?), then this component is secure. Always activate the server side verification and set the `targetPageURL` properly if possible.


## Possible Enhancements in the future
- Passing the `targetPageURL`and `sitekey` to the iframe as query parameters.
- Passing the `secret-key` from the aura component to the apex class as string parameter.
