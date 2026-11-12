# Baller Path Android build brief

Use this brief and `baller-path-android-fixture.json` with the existing Baller Path project in Google AI Studio. The fixture contains fictional data. Its fields are a proposed Android view model and do not describe a live API response.

## What Baller Path does

Storm Basketball uses Baller Path to continue coaching between sessions. Coach Keith chooses one focus with the player, gives them a practice task with a reason, reads what they noticed, and tells them what to try next. The family can see the focus, published work, and feedback. The player can continue with available work while waiting for a coach response. The coach decides when access opens and when feedback is published.

## Next build in AI Studio

Extend the existing Kotlin and Compose project. Keep its four destinations, local storage, visual style, and fictional-player label. Use the project's existing models and repository where possible. Translate the supplied fixture into those models.

1. Today shows one primary action for the current state. Use Start practice, Resume practice, Finish reflection, Open work, or Read feedback as appropriate. When a reflection is in review, acknowledge that it was saved and offer another task only if one is available.
2. The existing optional step timer supports start, pause, and resume. Save the current step and timer state through app backgrounding. The player can move to the next step when ready. A timer finishing never sends a reflection or marks the assignment complete.
3. Save an unfinished reflection locally and restore it after the app restarts. Keep the submitted reflection distinct from the draft. In Work + Feedback, show Awaiting Coach after submission. Provide a clearly labeled demo control to reveal the prewritten sample response, then show the response and one next adjustment together.
4. On Account, let the tester switch between fictional cases and reset the demo. Make the sample state visible on each relevant screen. Keep this build local with sample data and existing screens.

Deliver a buildable project, a list of edited files, and results from the emulator. On a 360 dp wide screen, confirm that the main Today action is visible and readable. Check timer pause and resume, back navigation, draft recovery after restart, and the two distinct states for waiting and feedback ready. Repeat with larger system text and ensure key actions can still be reached. Try the final build on a real Android phone before involving families.

## Existing web implementation and what Android can reuse

| Product responsibility | Existing implementation | First Android integration |
| --- | --- | --- |
| Sign-in and identity | `/auth/google` and `/auth/callback` use Supabase Google OAuth. The callback creates a Baller Path session in Cloudflare D1 and sets an HTTP-only browser cookie. `app/chatgpt-auth.ts` resolves that session and preserves coach identity. | Add a supported Android sign-in and a server-verified session for Android. Map the verified Supabase user to the existing Baller Path account. Keep Android sign-in independent of the browser cookie. |
| Family ownership and player selection | `/api/account` returns players connected to the signed-in family. | Return a minimal list of owned players with active or setup status. Check ownership server-side whenever a player is selected. |
| Today, Plan, and Work status | `/api/player-workflow?playerId=…` uses `app/player-workflow-service.ts` and `app/workflow-state.ts` for published focus, work, feedback and next action. | Reuse the existing service and return a family-only view through a versioned read endpoint. Preserve setup restrictions, general work, and pending feedback. |
| Reflection and feedback | `/api/assignments` reads published assignments and saves reflections. Coach review publishes feedback and emails the family. | Start with read-only work and feedback. The existing reflection write can overwrite earlier feedback on resubmission, so fix history preservation and duplicate-submit handling before enabling native writes. |
| Coach operations and onboarding | Coach Tasks, Players, invitations, activation and registration are working web flows. | Keep these on the web for the first Android pilot. Add mobile coach tools after the family flow works on phones. |

The web routes above use the current website session. An Android app with a Supabase session will not automatically be accepted by them. Build the native session and read endpoint in a test environment first. Verify two different family accounts, expired sessions, sign-out, and pending access. The client receives only data the server has authorized. Keep database credentials, email credentials, and any model API credentials on the server.

## How to move from demo to distribution

1. Open the existing Baller Path project in Google AI Studio. Add this brief and the fictional fixture. Iterate in the emulator and install the build on your phone.
2. Export the Android project ZIP so it can be checked in Android Studio and placed in a separate GitHub repository. Verify the package name, Gradle build, and signing setup before distributing the first build.
3. Use Google Play internal testing when the phone build is stable. Keep the demo on fictional data until the authenticated read-only family experience passes the two-account test. Public Play Store distribution is a later release in Play Console.

Official references: [Google AI Studio Android](https://ai.google.dev/gemini-api/docs/aistudio-android), [Google Play testing](https://support.google.com/googleplay/android-developer/answer/9845334), [Supabase native sign-in](https://supabase.com/docs/reference/kotlin/auth-signinwithidtoken), and [Android App Links](https://developer.android.com/training/app-links).
