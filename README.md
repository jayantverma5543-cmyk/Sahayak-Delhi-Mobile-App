# Sahayak-Delhi-Mobile-App
An Android citizen-assistance app for Delhi, bringing emergency support, healthcare guidance, local services, and AI-assisted information into one accessible platform

An AI/ML-powered starting point for healthcare discovery and civic support

AI/ML Track · Android

Sahayak Delhi is an Android prototype that brings several everyday and urgent-help workflows into one mobile experience: emergency guidance, hospital and doctor discovery, first-aid information, rule-based symptom guidance, water-tanker workflows, exam counselling, voice-assisted interactions, universal search, and form autofill.
The project aims to reduce the effort of finding the right service and make information journeys easier to navigate. Its planned TinyFish integration adds a live-web evidence layer so users can discover current pages, inspect extracted content, and open the original source.

Contents
1. The problem
2. Our solution
3.Features in the current prototype
4. Who it is for
5. AI/ML approach
6. TinyFish integration plan
7. Architecture
8. Tech stack
9. Run the Android app
10. Configuration and secrets
11. Testing checklist
12. Limitations and safety
13. Roadmap
14. Contributing
---
1. The problem
Finding a suitable next step for healthcare and civic needs can require switching between separate apps, search results, forms, and service websites. This can be particularly difficult when someone is stressed, has limited time, is unfamiliar with official websites, or would benefit from voice input and reduced typing.
Sahayak Delhi explores a unified mobile entry point for common journeys. A second challenge is information trust: a useful answer should not merely sound convincing; people should be able to inspect where web information came from and when it was retrieved.

2. Our solution
Sahayak Delhi combines local mobile workflows with a planned source-backed web layer:
Start in one app rather than searching for a different starting point for every need.
Choose a relevant workflow such as hospitals, first aid, water services, or student support.
Reduce repeated input with voice-related interactions, parsing, and profile concepts.
Add live-web evidence through TinyFish—once integrated—to retrieve current information, retain the source URL and retrieval time, and let users open the original page.
Keep people in control: show uncertainty, request confirmation before consequential actions, and avoid presenting symptom guidance as a diagnosis.
Features in the current prototype
The following describes code and UI found in the supplied project archive. Individual flows should still be built and tested on a device before being described as production-ready.

3. Feature	Current prototype scope
Emergency support	Emergency screen/SOS dialog and local voice panic-phrase matching logic
Hospital discovery	Hospital search/filter UI, distance-related logic and a Delhi fallback location
First-aid information	First-aid content screens
Symptom guidance	Rule/keyword-based guidance; not a validated diagnostic model
Doctor discovery	Doctor/consultation discovery screen
Water-tanker workflows	Supplier search/filter and booking-related local data models
Exam counselling	Exam counsellor workflow
Universal search	Search overlay and local routing/heuristic logic
Voice-related UX	Local phrase detection and voice/permission-related settings
Form autofill	Heuristic extraction of likely fields, with a confirmation UI
Saved profile concepts	Local profile/persistence-related structures
Alerts and settings	Predictive-alert UI and permission/settings flows
Local data	Room entities, DAO and database components
Data-quality note: Some information is bundled or static, and the form parser in the supplied archive contains sample fallback values. Verify emergency contacts, hospital details, provider records, service availability and clinical content before relying on them. Replace sample personal-data defaults with empty or user-confirmed values.

4. Who it is for
Delhi residents looking for a starting point for civic and local service information.
Patients and caregivers looking for healthcare discovery and basic first-aid information.
Households exploring water-tanker or related local-service workflows.
Students looking for exam-related counselling or information.
People who benefit from reduced typing or voice-assisted interaction.
These are intended users, not a claim that the app has already been clinically or statistically evaluated with them.

5. AI/ML approach
The current code uses lightweight local logic in several places:
Intent/workflow routing: heuristic matching to direct queries to relevant app flows.
Voice panic-phrase detection: matching recognized phrases against a local phrase list.
Form parsing: rule/regex-based extraction of possible fields such as name, age, phone, and locality.
Symptom guidance: keyword/rule-based mapping to predefined guidance.
TinyFish-named model code: placeholder/heuristic behavior rather than demonstrated live TinyFish web calls or validated medical inference.
These techniques are useful for prototyping, but they should not be described as a trained or clinically validated ML model. A future evaluation should measure intent-routing accuracy, parsing precision/recall, false-positive voice triggers, task completion, and the quality/freshness of retrieved web evidence.

6. TinyFish integration plan
TinyFish is intended to provide the live-web layer. The planned design uses all four capability areas, but they are not verified as connected in the current source archive.
- Search API — discover sources
Send a user-approved, non-sensitive query from the app to our backend.
Call the documented TinyFish Search endpoint from the backend.
Return candidate page titles, URLs and available metadata.
Prefer official and authoritative sources for public-service information.
- Fetch API — extract useful page content
Select an appropriate result and fetch its content using the documented TinyFish Fetch capability.

Extract a compact text representation and relevant metadata.
Preserve the canonical source URL and retrieval timestamp.
Handle blocked pages, stale information, timeouts, empty results and malformed responses.
3. Web Agent — multi-step tasks
For tasks that genuinely require several website interactions, route a narrowly scoped goal to TinyFish Web Agent. Keep the user informed about what is being attempted. Do not submit forms, book services, or take consequential actions without explicit user confirmation.
4. Cloud browser infrastructure — browser-driven workflows
Use the browser infrastructure supported by the team's TinyFish account and current documentation for websites that cannot be handled reliably through simple Search + Fetch. If supported by the chosen setup, browser tooling may involve Playwright or CDP. Apply domain allowlists, timeouts, action limits, and careful handling of cookies or authenticated sessions.
Evidence-aware response format
A successful live-web result should present:
A short answer grounded in retrieved content.
The source title and clickable original URL.
Retrieval time (and source publication/update time when reliably available).
A note when information is missing, conflicting or uncertain.
A user-controlled action to open the original source.
First end-to-end milestone
Start with a non-sensitive query such as:
> “Find the official Delhi Jal Board page for water-related citizen services.”
The expected demo flow is:
`Android app → backend proxy → TinyFish Search → TinyFish Fetch → source-backed result → URL + retrieval time → user opens source`
Only call this a live TinyFish demonstration after the workflow has succeeded against the real API during testing.
Confirm the API contract first
Before implementation, check the current TinyFish documentation and your account for the actual base URL, authentication method, endpoint paths, request/response schemas, quotas, and supported browser tools. Do not infer or invent API details from examples for other services.

7. Architecture
Current prototype
Android UI: Kotlin and Jetpack Compose.
State and workflows: ViewModel, StateFlow/coroutines and local logic.
Persistence: Room database components.
Data: bundled/local records and workflow models.
Proposed live-web architecture
```text
┌──────────────────────────────┐
│ Android app                  │
│ Compose UI + user consent    │
└──────────────┬───────────────┘
               │ HTTPS: minimal query
               ▼
┌──────────────────────────────┐
│ Backend proxy                │
│ Auth, validation, rate limits│
│ Provider key in server env   │
└──────────────┬───────────────┘
               ▼
┌──────────────────────────────┐
│ TinyFish live-web layer      │
│ Search → Fetch               │
│ Web Agent / browser as needed│
└──────────────┬───────────────┘
               ▼
┌──────────────────────────────┐
│ Evidence-aware response      │
│ Answer + source URL + time   │
│ Uncertainty + user control   │
└──────────────────────────────┘
```
The backend proxy is a planned integration component; the current archive does not establish that this complete architecture is already deployed.

8. Tech stack
Language: Kotlin
Platform: Android
UI: Jetpack Compose and Material 3
State: ViewModel, StateFlow and coroutines
Local persistence: Room and KSP
Networking dependencies: Retrofit/OkHttp are declared in the project
Other dependencies: Firebase-related Gradle configuration is present
A declared dependency does not by itself mean a production backend or Firebase project is configured.

9. Run the Android app
Requirements
Android Studio with a compatible Android SDK and Gradle/JDK setup.
Android device or emulator.
Network access for Gradle dependency resolution.
The supplied project folder, `Sahayak-Delhi-app-main`.
Steps
Clone the repository or download it as a ZIP.
Open the `Sahayak-Delhi-app-main` project root in Android Studio.
Allow Gradle sync to finish.
If Android Studio reports a missing SDK or plugin, install or configure the version it requests.
Start an emulator or connect a test device with USB debugging enabled.
Select the `app` run configuration and launch it.
Walk through the test checklist below.
The supplied archive declares `minSdk` 24 and a recent `compileSdk` setting. A clean build has to be verified in your own environment before claiming build success. If you encounter a build error, capture the full Gradle output and document the fix.

10. Configuration and secrets
Never commit real API keys, passwords, service-account files, signing keystores, or personal information.
Do not put a TinyFish provider key inside Kotlin code, `BuildConfig`, Android resources, or the APK.
Keep provider credentials in backend environment variables or a secrets manager.
Validate requests on the server, add rate limiting and timeouts, and redact secrets from logs.
Keep `.env` files and local machine configuration out of Git. Commit only a safe `.env.example` with placeholder values.
Ask consent before sending user data to third-party websites; minimize the data sent.
Android encrypted storage can protect local app data, but it does not make a third-party API key embedded in a distributed APK secret.

11. Testing checklist
Android prototype

[ ] Gradle sync succeeds on a clean checkout.

[ ] App builds and launches on an emulator or device.

[ ] Emergency UI and permissions behave as expected without accidental real calls.

[ ] Hospital search/filter works and the location-denied fallback is clear.

[ ] First-aid and symptom guidance display a clear non-diagnosis boundary.

[ ] Doctor and exam-counsellor screens handle no-result/error states.

[ ] Water-tanker search, filters and booking-related states behave as expected.

[ ] Universal search routes representative queries correctly.

[ ] Autofill suggestions can be reviewed and edited before use.

[ ] Sample personal data is removed or explicitly user-confirmed.

[ ] Bundled civic/provider information is verified and dated.

TinyFish integration (pending until implemented)

[ ] Backend calls the real TinyFish Search API successfully.

[ ] Fetch retrieves page content from a selected result.

[ ] UI shows the returned source URL and retrieval timestamp.

[ ] Network errors, rate limits, empty results and invalid responses are handled.

[ ] API keys are never exposed in the app or logs.

[ ] The demo can be repeated live without mocked responses.

[ ] Web Agent/browser tasks are tested separately before being advertised as working.

12. Limitations and safety
This is a prototype for navigation and information access, not a medical diagnostic device, emergency dispatch system, or official government service.
Symptom guidance is rule-based and must not replace a clinician's assessment.
In an emergency, contact the appropriate official emergency service directly; do not rely on this prototype as a dispatch channel.
Hospital, doctor, supplier, availability and contact information must be checked against authoritative, current sources.
Do not send health, emergency or identifying information to third-party websites without explicit user consent and a clear need.
The current source does not prove a live TinyFish integration. Planned functionality must remain labelled as planned until tested.
Do not publish claims about user impact, model accuracy, build success or API performance without measurements.

13. Roadmap

-Phase 1 — Stabilise the prototype
Verify clean build and core screen flows.
Remove sample personal-data fallbacks.
Add empty, loading and error states.
Audit local records and emergency/medical wording.

-Phase 2 — Ship one TinyFish vertical slice
Build a backend proxy with server-side credentials.
Integrate Search and Fetch using the official API contract.
Show a source-backed answer, URL and retrieval timestamp.
Add automated tests for parsing, errors and response handling.

-Phase 3 — Demonstrate and evaluate
Record a repeatable live demo.
Measure task completion, source relevance, retrieval latency and failure rate.
Ask a small group of test users to complete representative tasks and document findings.

-Phase 4 — Expand carefully
Add Web Agent and browser-based workflows when justified.
Improve multilingual and accessibility support.
Add monitoring, audit logs and consent controls.
Integrate verified official-service sources where permitted.

14.Contributing
Contributions and issue reports are welcome. For a useful bug report, include:
Device/emulator and Android version.
Steps to reproduce.
Expected and actual behavior.
Relevant, redacted logs or screenshots.
Whether the issue occurs with location/microphone permissions enabled.
Please do not include API keys, personal health information, phone numbers or other sensitive data in issues.

Project: Sahayak Delhi  
Track: AI/ML
