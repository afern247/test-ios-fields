*Reported Apple iOS bug*

## Summary

When My Card (iPhone contact card) contains exactly one valid email address, iOS never offers that email as a QuickType suggestion for an HTML email field inside WKWebView.

Instead, QuickType displays “Hide My Email”. Tapping that candidate inserts the literal text “Hide My Email” into the field instead of inserting the email from My Card or opening the genuine Hide My Email system interface.

Adding a second distinct email address to My Card changes the behavior. QuickType then begins offering a real email address. The original and preferred email should be suggested even when it is the only email in My Card.

Environment

Device: iPhone 15
iOS: 26.6.1 (23G83)
Xcode: 26.6 (17F113)
Web view: WKWebView
Test URL: https://afern247.github.io/test-ios-fields/

The input uses:

type="email"
inputmode="email"
autocomplete="email"

Steps to reproduce

1. Set the device’s My Card in Contacts.
2. Add exactly one valid email address to My Card.
3. Launch the attached reproduction app.
4. Tap Continue to present the test page inside WKWebView.
5. Tap the Email address field.
6. Observe the QuickType suggestions.
7. Tap “Hide My Email”.

Actual result

1. The only email stored in My Card is never suggested.
2. QuickType displays “Hide My Email” instead.
3. Tapping it inserts the literal string “Hide My Email” into the email field.
4. No Hide My Email system interface is presented.

Expected result

1. QuickType should offer the valid email stored in My Card even when My Card contains only one email.
2. If the genuine Hide My Email action is also available, it should be presented as a separate action.
3. Selecting the genuine action should open Apple’s Hide My Email interface.
4. The localized action title “Hide My Email” must never be inserted as an email value.

Controlled comparison

1. Keep the page, app, WKWebView, device, and operating system unchanged.
2. Add a second distinct email address to My Card.
3. Focus the same input again.
4. QuickType now offers a real email address.

This demonstrates that the result changes based only on the number of email addresses in My Card.

Safari comparison

Opening the same URL in Safari correctly offers contact information. The failure occurs through the non Safari WKWebView text input path.

Debugger confirmation

A breakpoint on:

-[WKContentView(WKInteraction) insertText:]

stops after tapping the incorrect candidate.

The value passed by UIKit is:

Hide My Email

The top of the call stack is:

WebKit
-[WKContentView(WKInteraction) insertText:]

UIKitCore
-[UIKBInputDelegateManager insertText:updateInputSource:]

UIKitCore
-[UIKBInputDelegateManager insertText:]

This confirms that UIKit delivers the localized action title to WKWebView as ordinary insertion text before the webpage receives it.
