**Question:** How does opening a URL in the browser opens up the app in mobile?

This approach is called "Deep linking".

## What?

Redirecting user from a URL to any specific page inside the app.

Example: when you open a github url from somewhere, and it opens the link in the app(instead of a browser).

## How does it work?

Deep link is just a URL, so where should we put this URL(and will it work automatically)?

Two possiblities:

- User has the app: Simple open the app when URL is clicked
- User didn't install the app: Redirect to download app store, and then after install, redirect to specific app page(this is called **deferred deep linking**)

There's also something called "deep linking engine" which does main the job.

## How does it work?

Deep linking revolves around universal links, app links and URI schemes(explained below).

Hard to find a proper tech blog on it. Most of the google search results are for marketing purpose.

First, we need to register a URI scheme(example: `http`, `https`, `mailto`, etc.). For example, to open a twitter app, we can defined a URI scheme as `twitter://`.

## Deep linking

Universal links(ios) and app links(android) are basically the same mechanism.

> Keep in mind that Universal Links and App Links are only for opening an app that’s already installed. If the app is not installed, you can set up a process called **deferred deep linking with a deep linking provider**.

Apparently firebase calls it dynamic linking.

There's deep links, web links and android app links.

> App links and deep links are both ways to link to specific content within a mobile app.
>
> App links are a type of deep link, but with an added layer of verification to ensure that the app is authorized to handle the link. This provides a more secure user experience, as it prevents phishing and malicious apps from intercepting and hijacking links.
>
> Deep links, on the other hand, are simply links that point to a specific location within an app. They can be used to open specific pages, or to pass data into the app when it launches. Deep links can be used to link to content within both installed and web-based apps, whereas app links are only supported in installed apps.

### Universal links

Universal links are a type of deep link for iOS apps.

> both universal links and app links serve the same purpose, but universal links are specific to iOS and have different verification and configuration requirements.

## Dynamic links

Specific to firebase.

> Dynamic links are a type of deep link provided by Firebase. Dynamic links provide a more flexible and customizable solution for deep linking compared to app links and universal links, but require a Firebase integration.

## Steps

Define URI -> Create deep link -> Handle deep links(on backend and app)

## Links:

- Flutter docs on deep linking: https://docs.flutter.dev/development/ui/navigation/deep-linking
- Deep linking 101: https://www.appsflyer.com/resources/guides/deep-linking-101/
- Wikipedia: https://en.wikipedia.org/wiki/Mobile_deep_linking
- Most people opt for external services instead of making it in-house: https://firebase.google.com/products/dynamic-links
- Android docs: https://developer.android.com/training/app-links

## In short

Deep linking is a method to link a URL to some content in your app. You define those URL using custom URI schemes(for example: `twitter://some-link-to-be-opened-in-app`).

Universal links and app links are basically the same(with some exceptions), they are named platform specific. Firebase calls it dynamic linking(i guess it's kinda more powerful than deep linking).
