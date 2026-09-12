# MSAL Sample — Swift (UIKit)

A working Azure AD B2C sign-in sample for iOS, in **UIKit**. Sign up or sign in against a B2C user flow, get an access token, call a protected API with it, refresh it, edit the profile, and sign out.

> SwiftUI version: [MSAL-Sample-SwiftUI](https://github.com/iamuhammadkhan/MSAL-Sample-SwiftUI).

## Run it

```sh
git clone https://github.com/iamuhammadkhan/MSAL-Sample-Swift
cd MSAL-Sample-Swift
pod install          # Pods are committed, so this is optional
open Testing-MSAL.xcworkspace
```

Open the **workspace**, not the project. Build and run — it works out of the box against Microsoft's public demo tenant (`fabrikamb2c.onmicrosoft.com`), so you can try the whole flow without an Azure account.

Requires iOS 14+ and Xcode 13+.

## Pointing it at your own tenant

The constants are at the top of [`ViewController.swift`](Testing-MSAL/ViewController.swift):

```swift
let kTenantName           = "fabrikamb2c.onmicrosoft.com"
let kAuthorityHostName    = "fabrikamb2c.b2clogin.com"
let kClientID             = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6"
let kSignupOrSigninPolicy = "b2c_1_susi"
let kEditProfilePolicy    = "b2c_1_edit_profile"
let kResetPasswordPolicy  = "b2c_1_reset"
```

Then set the URL scheme in [`Info.plist`](Testing-MSAL/Info.plist) to `msal<your-client-id>`. MSAL constructs its redirect URI as `msal<client-id>://auth` when you pass `redirectUri: nil`, and sign-in fails at the redirect if that scheme is not registered — the most common reason a first B2C integration does not work.

## The parts of B2C that trip people up

**A "policy" is an entire user flow.** B2C hosts a separate UI per flow — sign-up/sign-in, edit profile, reset password — and each is its own authority URL. Hence three policy constants, not one.

**The authority URL encodes the policy:** `https://<host>/tfp/<tenant>/<policy>`. Each token call must target the authority belonging to the flow it is part of.

**Silent first, interactive as fallback.** `acquireTokenSilent` uses the cached refresh token with no UI. When it fails with `MSALError.interactionRequired`, fall back to `acquireToken` to present the web view. `refreshToken()` shows the complete pattern.

**Password reset arrives as an error.** B2C signals it by failing sign-in with a specific code, which you catch and answer by launching the reset policy.

## Credits

Configuration and B2C flow follow [Microsoft's official iOS B2C sample](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal).

Built for a client who was stuck on B2C, and published in case it helps someone else.

## License

MIT.
