<!-- START_METADATA
---
title: Vipps landing page
pagination_next: null
pagination_prev: null
---
END_METADATA -->

# The Vipps landing page

When a user is directed to the `url` sent in the response to
[`POST:/ecomm/v2/payments`](https://vippsas.github.io/vipps-developer-docs/api/ecom#tag/Vipps-eCom-API/operation/initiatePaymentV3UsingPOST),
they will either be taken to Vipps or to the Vipps landing page:

* In a mobile browser, the Vipps app will automatically be opened with app-switch.
  The result is the same for the `vipps://` and the `https://` URLs.
* In a desktop browser, the landing page will prompt the user for the phone number
  (the number may also be pre-filled, see below).
  The user enters or confirms the phone number. Then on their phone, the user
  gets a push notification and Vipps then prompts for confirmation.

![The Vipps landing page](images/vipps-flow-landing-page.png)

The Vipps landing page is mandatory and provides a consistent and recognizable user experience
that helps guide the user through the payment flow.
Our data shows that the landing page gives a higher success rate and lower drop-off,
because the users get a familiar user experience and know the payment flow.
In this way, Vipps takes responsibility for helping the user from the browser to the app,
and to complete the payment in a familiar way.

The user's phone number can be set in the payment initiation call:
[`POST:/ecomm/v2/payments`](https://vippsas.github.io/vipps-developer-docs/api/ecom#tag/Vipps-eCom-API/operation/initiatePaymentV3UsingPOST).

The user's phone number is remembered by the user's browser,
eliminating the need for re-typing it on subsequent purchases.

**Important:** Never show the Vipps landing page inside an iframe.
That will make it impossible for the user to reliably be redirected back to the
merchant's website, and result in a lower success rate.
In general: Any "optimization" of the payment flow may break the Vipps payment flow - if not today, then later.

## Generating a QR code to the Vipps landing page

If you have user-facing display, you may want to generate a QR code based on the
landing page URL, instead of asking the user for their phone number. Scanning
the QR code will take the user directly to the payment in the Vipps app.

![Demo QR code](images/demo-qr.svg)

This is done in cooperation with the Vipps QR API. See
[One-time payment QR](https://github.com/vippsas/vipps-qr-api#one-time-payment-qr)
in the Vipps QR API guide for more details about this and other QR services.

See the
[Quick start](https://github.com/vippsas/vipps-ecom-api/blob/master/vipps-ecom-api-quick-start.md)
for step-by-step examples of generating QR codes and short links for one-time payments initiated from the eCom API.

## Skip landing page

*This functionality is only available for special cases.*

Skipping the landing page is reserved for physical points of sale and vending
machines where no display is available.

This feature must be specially enabled by Vipps for an eligible sale unit and
the sale unit must be whitelisted by Vipps.

If the `skipLandingPage` property is set to `true` in the
[`POST:/ecomm/v2/payments`](https://vippsas.github.io/vipps-developer-docs/api/ecom#tag/Vipps-eCom-API/operation/initiatePaymentV3UsingPOST)
call, it will cause a push notification to be sent immediately to the given
phone number, without loading the landing page.
If the sale unit is not whitelisted, the request will fail and an error message will be returned.

See FAQ:

* [Is it possible to skip the landing page](https://github.com/vippsas/vipps-ecom-api/blob/master/vipps-ecom-api-faq.md#is-it-possible-to-skip-the-landing-page)
* [How can I check if I have skipLandingPage activated?](https://github.com/vippsas/vipps-ecom-api/blob/master/vipps-ecom-api-faq.md#how-can-i-check-if-i-have-skiplandingpage-activated)

**Important:** When using `"skipLandingPage": true`:

* The user is not able to provide a different phne number for completing the payment.
* It is crucial to use the correct format for the user's phone number.
  If not, the payment will fail.
* The user is not sent to a `fallback` URL after completion of the payment.
  The "result page" is just the confirmation in Vipps.
  The `fallback` URL sent in the API request can simply be the website URL.