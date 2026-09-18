# DEPLAY MAX — releases for partner studios

DEPLAY MAX is one subscription a parent buys, which unlocks the apps of several studios on the family's tablet.
Your app asks DEPLAY MAX one question — *does this family's subscription unlock me?* — and unlocks if the answer is
yes. This repository is where you download everything you need to ask it.

Everything ships as a **release**. Take the newest one: [**Releases →**](https://github.com/DEPLAY-SOFTWARE-BV/deplay-max-releases/releases).

## Start here: what are you building?

| You are building | Download from the release | Read, in the release's `README.md` |
| --- | --- | --- |
| A native Android app (Kotlin or Java) that checks on the device | `deplay-max-sdk-<version>.aar` | *The SDK in five minutes*, then *Integrating the SDK* |
| A Unity game | `deplay-max-unity-<version>.tgz` | *The Unity package*, then *The SDK in five minutes* |
| A backend that asks about a parent by e-mail address | nothing — you call our HTTPS API | *When to check*, and the route 2 rows of the error table |
| A backend that verifies a signed token your app received | `deplay-max-examples-<version>.tar.gz` → `route3-verify-node` | *Route 3: a token for your own server* and *Verifying a route 3 token* |
| Trying it out before you write code | `deplay-max-<version>-sandbox-debug.apk` | *Download*, and the sandbox notes in the release description |

Unsure which route is yours? One line each:

1. **On the device.** Your app binds to DEPLAY MAX and gets yes or no. Works offline, you need no backend.
2. **Server to server.** Your backend asks ours about a parent's e-mail address. For studios that already hold it.
3. **Signed token.** DEPLAY MAX hands your app a token, your server verifies it against our public keys.

Route 1 is the common case. Nothing stops you combining them.

## Every release holds the same six files

| File | What it is |
| --- | --- |
| `deplay-max-sdk-<version>.aar` | the SDK: Java 8 bytecode, no dependencies, the binder interface inside it |
| `README.md` | one document: the quick start, the full integration guide and the binder contract |
| `deplay-max-unity-<version>.tgz` | the Unity package, installed through Unity's package manager |
| `deplay-max-examples-<version>.tar.gz` | working reference clients for all three routes, including a Node token verifier |
| `deplay-max-<version>-sandbox-debug.apk` | DEPLAY MAX itself, built for the sandbox, to install on a test tablet |
| `SHA256SUMS` | checksums of the five files above |

Check what you downloaded before you use it:

```bash
sha256sum -c SHA256SUMS
```

## What DEPLAY gives you, and what DEPLAY needs from you

DEPLAY gives you, per environment (sandbox first, production when you go live):

- the **package name(s)** of your app registered, so a subscription actually unlocks them;
- an **API key**, if you use route 2 or route 3;
- **two test accounts**, one with a running subscription and one without;
- the **signing fingerprints** of the DEPLAY MAX app, which are already compiled into the AAR.

DEPLAY needs from you:

- the **package name of every app** that should be unlocked, exactly as it appears in the Play Store;
- a **GitHub account name** per developer who needs these downloads, because this repository is private;
- for route 2 or 3, the **server** that will call us, so the key is issued to the right studio.

## Five rules that decide whether your integration is correct

1. **Unlock if your own access is valid OR DEPLAY MAX says yes** — never the other way round. A parent who bought
   your app keeps it; the subscription is an extra door, not a replacement.
2. **An error is never a "no".** If the answer does not arrive within three seconds you get `UNAVAILABLE`, which is
   normal on a cold start: your own access decides, and your last cached answer stands until it expires.
3. **Check every time your app comes to the foreground**, even if you checked an hour ago. Those checks are how
   usage is measured, and DEPLAY MAX never pushes you a signal that something changed — there is nothing to
   receive and nothing to build. A check made with internet also refreshes DEPLAY MAX's own status, so a change
   shows up in your *next* check.
4. **Verify our signature.** Check the package name and the certificate fingerprint. The SDK does it for you.
5. **No price, no subscribe button, no payment link anywhere in your app.** When the answer is no and the family
   has no access through you, send them to DEPLAY MAX with the intent `nl.deplay.max.OPEN`; it shows the right
   screen. Exceptions exist only where DEPLAY has agreed one with you in writing.

Rules 1, 2 and 5 are the ones that break real families when they are wrong: the first two lock out paying parents,
the last one is what app store review will reject you for.

## Where to test: the sandbox

| | sandbox | production |
| --- | --- | --- |
| API base URL | `https://sandbox.api.deplaymax.com` | `https://api.deplaymax.com` |
| Token issuer (`iss`) and JWKS | the same host, `/.well-known/jwks.json` | the same host, `/.well-known/jwks.json` |
| Route 2 endpoint | `POST /api/partner/v1/status` | the same path |
| API key | issued per studio, per environment | issued per studio, per environment |

Build against the sandbox first. The sandbox DEPLAY MAX app in the release is a debug build, so the SDK only
accepts it after you turn that on explicitly — `DeplayMax.setAllowDebugSignature(true)`, or
`DeplayMaxBridge.AllowDebugSignature()` in Unity. Never ship that switch enabled.

## What is ready today

The current release is a **sandbox prerelease**. The SDK in it trusts only the sandbox build of DEPLAY MAX, so it
is exactly right for building and testing your integration, and must not ship in a Play Store release of your app.
The production SDK follows once the DEPLAY MAX app is published; it is the same API, and the switch is a version
bump, not a rewrite.

## Your agreement, and this repository

Your studio's integration agreement with DEPLAY — the appendix DEPLAY sends you, *Bijlage A* in Dutch — is what
binds; these documents are the engineering detail underneath it. Where the two disagree, the agreement wins, and
the release `README.md` flags the places where it goes further than the agreement does. **DEPLAY sends you that
agreement directly and it is not published here**, so that there is one copy and it is the signed one. If you do
not have it, or are unsure which version you hold, ask DEPLAY before you build against anything below.

Anything unclear, missing or contradictory: tell your contact at DEPLAY rather than guessing. A wrong guess here
shows up as a family that cannot open your app.
