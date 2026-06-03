# Codex Prompt: Recreate Npay Animation POC

Use the following prompt in a fresh Codex session to recreate this project on another laptop.

```text
You are Codex working in a new empty or partially empty workspace. Recreate an Android Jetpack Compose proof of concept named "Npay Animation POC" for previewing, tuning, and handing off native POS payment-status icon animations.

Primary product goal:
Build a native Android Compose tool that helps a design engineer tune motion specs for SVG-derived payment status icons and then hand those specs to Android developers in a clear, production-shaped format. The app is not a marketing page. It is an internal design-to-development motion preview and handoff tool.

Target stack:
- Android application module only.
- Package/namespace: com.npay.animationpoc
- Kotlin + Jetpack Compose + Material 3.
- AGP: 8.13.2
- Kotlin: 2.0.21
- Compose BOM: 2025.01.00
- compileSdk: 35
- targetSdk: 35
- minSdk: 26
- Java/Kotlin JVM target: 11
- Main device assumption: Android 12 POS hardware, so keep animations calm and efficient.
- Do not use Lottie. Use native Compose animation APIs only.

Gradle dependencies:
- androidx.core:core-ktx 1.15.0
- androidx.lifecycle:lifecycle-runtime-ktx 2.8.7
- androidx.activity:activity-compose 1.10.0
- Compose BOM 2025.01.00
- androidx.compose.ui:ui
- androidx.compose.ui:ui-graphics
- androidx.compose.ui:ui-tooling-preview
- androidx.compose.material3:material3
- debugImplementation androidx.compose.ui:ui-tooling

Required project tree:
app/src/main/java/com/npay/animationpoc/
  MainActivity.kt
  ui/
    PaymentAnimationComparisonApp.kt
    motionspec/
      PaymentMotionSpec.kt
    preview/
      PaymentStatusPreviewScreen.kt
    statusanimation/
      AnimatedStatusIcon.kt
      StatusIconAnimationSpec.kt
    statusicons/
      StatusIconAsset.kt
    theme/
      Theme.kt
      Type.kt

app/src/main/assets/status-icons/
  README.md
  catalog.json
  svg/
    checkmark.svg
    error.svg
    fail.svg
    payment-declined.svg
    loading.svg

tools/
  import-status-icons.mjs

Core app behavior:
- MainActivity enables edge-to-edge and sets NpayAnimationPocTheme { PaymentAnimationComparisonApp() }.
- PaymentAnimationComparisonApp only renders PaymentStatusPreviewScreen.
- PaymentStatusPreviewScreen has two workspace views:
  - Design: tune and replay the animation.
  - Handoff: inspect the approved motion spec, validation messages, JSON output, Compose usage snippet, and implementation reference.
- The Design/Handoff mode is a segmented switch.
- Keep the first screen functional, not explanatory. Use practical controls and a large centered icon preview.

Payment states:
- Success: checkmark.svg, default color #074D31, recommended preset Trace.
- Error: error.svg, default color #B42318, recommended preset Pop.
- Failure: fail.svg, default color #B54708, recommended preset Fade.
- Decline: payment-declined.svg, default color #B42318, recommended preset Fade, same as Failure.
- Loading: loading.svg, default color #2D32AA, recommended preset SequentialAlphaPulse.

Important icon architecture:
- Source SVGs live in app/src/main/assets/status-icons/svg.
- catalog.json is the editable registry. It contains enumName, label, description, svg, defaultColor, and optional tracePaths.
- StatusIconAsset.kt is generated from catalog.json and the SVG files. Do not hand-edit it unless explicitly needed.
- Each generated StatusIconAsset contains:
  - label
  - description
  - sourceSvg
  - viewportWidth
  - viewportHeight
  - vectorPaths: List<StatusIconVectorPath>
  - tracePaths: List<StatusIconTracePath>
- StatusIconVectorPath stores pathData, fill, stroke, strokeWidth, strokeCap, strokeJoin.
- StatusIconTracePath stores pathData, stroke, strokeWidth.
- Filled SVGs need explicit centerline tracePaths in catalog.json if they should support Trace/path reveal. Do not try to animate a filled outline as the trace.
- Loading sequence order follows SVG path order. If rhythm is wrong, reorder paths in the source SVG or catalog/import stage.

SVG importer requirements:
- Implement tools/import-status-icons.mjs in Node ESM.
- It reads app/src/main/assets/status-icons/catalog.json and SVGs.
- It parses simple SVG path tags, viewBox, fill, stroke, stroke-width, stroke-linecap, stroke-linejoin.
- It supports hex colors, "none", and currentColor mapped to defaultColor.
- It warns for masks, clip paths, gradients, filters, transforms, styles/classes, and opacity because the importer preserves only simple path data.
- It writes app/src/main/java/com/npay/animationpoc/ui/statusicons/StatusIconAsset.kt.
- If catalog tracePaths exist, use them. Otherwise infer trace paths from stroked, non-filled SVG paths.
- For non-loading filled-only icons without tracePaths, warn that Trace/path reveal will fall back to final vector rendering.

catalog.json shape:
{
  "icons": [
    {
      "enumName": "Success",
      "label": "Success",
      "description": "Approved payment or completed customer action.",
      "svg": "checkmark.svg",
      "defaultColor": "#074D31",
      "tracePaths": [
        { "pathData": "<centerline circle-ish path>", "stroke": "#074D31", "strokeWidth": 4.25 },
        { "pathData": "<centerline checkmark path>", "stroke": "#074D31", "strokeWidth": 4.25 }
      ]
    },
    {
      "enumName": "Error",
      "label": "Error",
      "description": "Recoverable issue that needs customer or terminal attention.",
      "svg": "error.svg",
      "defaultColor": "#B42318",
      "tracePaths": [
        { "pathData": "<centerline circle path>", "stroke": "#B42318", "strokeWidth": 4.25 },
        { "pathData": "<x diagonal 1>", "stroke": "#B42318", "strokeWidth": 4.25 },
        { "pathData": "<x diagonal 2>", "stroke": "#B42318", "strokeWidth": 4.25 }
      ]
    },
    {
      "enumName": "Failure",
      "label": "Failure",
      "description": "Declined payment, cancelled flow, or blocked transaction.",
      "svg": "fail.svg",
      "defaultColor": "#B54708",
      "tracePaths": [
        { "pathData": "<triangle centerline>", "stroke": "#B54708", "strokeWidth": 4.25 },
        { "pathData": "<vertical warning line>", "stroke": "#B54708", "strokeWidth": 4.25 },
        { "pathData": "<warning dot>", "stroke": "#B54708", "strokeWidth": 6.65 }
      ]
    },
    {
      "enumName": "Decline",
      "label": "Decline",
      "description": "Declined card or payment response requiring another payment method.",
      "svg": "payment-declined.svg",
      "defaultColor": "#B42318"
    },
    {
      "enumName": "Loading",
      "label": "Loading",
      "description": "Processing payment or waiting for terminal response.",
      "svg": "loading.svg",
      "defaultColor": "#2D32AA"
    }
  ]
}

If the actual SVG files are available, use them. If the workspace does not contain the SVG source files, ask me to provide them before generating StatusIconAsset.kt. Do not invent a different visual set unless I explicitly approve placeholder icons.

Animation model:
Create StatusIconAnimationSpec.kt with:
- enum class StatusAnimationStyle(label):
  - Static("Static")
  - Trace("Trace")
  - Fade("Fade")
  - Pop("Pop")
  - Pulse("Pulse")
  - SequentialAppear("Sequential appear")
  - SequentialAlphaPulse("Sequential pulse")
- isLoadingSequentialStyle returns true for SequentialAppear and SequentialAlphaPulse.
- enum class StatusLoopMode(label):
  - Cycle("Cycle")
  - Yoyo("Yoyo")
- enum class StatusEasingPreset(label, easing):
  - Pos("POS", CubicBezierEasing(0.2f, 0f, 0f, 1f))
  - Emphasized("Emphasis", CubicBezierEasing(0.16f, 1f, 0.3f, 1f))
  - Linear("Linear", LinearEasing)
- data class StatusIconAnimationSpec:
  - style
  - durationMillis
  - delayMillis default 0
  - easingPreset default Pos
  - loop default false
  - loopMode default Cycle
  - pathReveal default true
  - startAlpha default 1f
  - endAlpha default 1f
  - startScale default 1f
  - endScale default 1f
  - transitionDurationMillis default 90
  - transitionEasingPreset default Pos

Recommended presets:
- Success: Trace, 420 ms, 40 ms delay, POS easing, pathReveal true.
- Error: Pop, 220 ms, Emphasized easing, pathReveal false, startAlpha 0f, startScale 0.92f.
- Failure and Decline: Fade, 180 ms, POS easing, pathReveal false, startAlpha 0f, startScale 0.98f.
- Loading: SequentialAlphaPulse, 1200 ms, Linear easing, loop true, pathReveal false, startAlpha 0.35f, endAlpha 1f.
- forStyle(Static): duration 0, pathReveal false.
- forStyle(Trace): 420 ms for Success, 320 ms for others, 40 ms delay, POS easing, pathReveal true.
- forStyle(Fade): 180 ms, POS easing, pathReveal false, startAlpha 0f.
- forStyle(Pop): 220 ms, Emphasized easing, pathReveal false, startAlpha 0f, startScale 0.9f.
- forStyle(Pulse): 700 ms, POS easing, loop true, loopMode Yoyo, pathReveal false, startScale 0.94f, endScale 1.04f.
- forStyle(SequentialAppear): 1000 ms, Linear easing, loop true, pathReveal false, transitionDurationMillis 90, transitionEasingPreset POS.
- forStyle(SequentialAlphaPulse): 1200 ms, Linear easing, loop true, pathReveal false, startAlpha 0.35f, endAlpha 1f.

Renderer requirements:
- Implement AnimatedStatusIcon(icon, animationSpec, replayKey, reducedMotion, modifier).
- Use Animatable<Float> from 0f to 1f for non-loop animations.
- For loop animations, repeatedly animate 0f to 1f; if loopMode is Yoyo, animate back to the loop start.
- Use delayMillis before each forward animation.
- If reducedMotion is true or style is Static, snap progress to 1f and render final vector.
- Convert StatusIconAsset.vectorPaths into an ImageVector for regular rendering.
- Convert vector paths into parsed Compose Path objects for sequential loading rendering.
- Convert tracePaths into measured paths for Trace rendering.
- Use PathMeasure.getSegment() for Trace:
  - Build Path objects from explicit tracePaths.
  - Measure length for each trace path.
  - Allocate progress by path length.
  - Draw each segment from 0f to length * localProgress.
  - Use StrokeCap.Round and StrokeJoin.Round.
- Do not render a second faded/static SVG behind Trace or SequentialAppear.
- Draw only one vector path list for sequential loading presets.
- SequentialAppear:
  - One SVG/vector instance only.
  - All paths are visible except one moving gap path.
  - Overall sequence timeline uses LinearEasing.
  - currentGapIndex = floor(loopProgress * pathCount).
  - nextGapIndex = (currentGapIndex + 1) % pathCount.
  - The inactive/gap path should not switch instantly.
  - During the local transition window, ease the current hidden path back in and the next path out using transitionEasingPreset.
  - transition fraction is transitionDurationMillis divided by per-path step duration, clamped roughly 0.05f..0.85f.
- SequentialAlphaPulse:
  - One SVG/vector instance only.
  - All paths stay visible.
  - Move alpha emphasis through SVG path order.
  - Resting alpha around 0.32f, active alpha 1f.
  - Overall loop uses LinearEasing.
- Avoid rotating/spinning loading animations.
- Avoid aggressive motion. POS feedback should feel calm, trustworthy, fast, and readable.

Preview UI requirements:
- Use Material 3 Card/SegmentedButton/Slider/Switch/Text.
- Root uses Scaffold with safe drawing insets, vertical scroll, horizontal padding 20.dp, vertical padding 24.dp.
- Header title: "Payment status motion".
- Subtitle: "Native Compose preview for SVG-derived status icons."
- Include a reduced motion switch.
- Workspace segmented control: Design, Handoff.
- Preview panel:
  - Shows selected icon label, description, Replay button.
  - Large centered AnimatedStatusIcon at 132.dp inside a 220.dp tall preview area.
- Design controls:
  - Payment state segmented button with small static icon preview and label for each state.
  - Animation style segmented control.
  - Easing segmented control, or "Transition easing" for SequentialAppear.
  - Duration slider:
    - 400..2000 ms for sequential styles
    - 0..1500 ms for other styles
    - round to nearest 50 ms
  - SequentialAppear transition duration slider:
    - 30..240 ms
    - round to nearest 10 ms
  - For non-sequential styles only:
    - Delay slider 0..500 ms, nearest 50 ms.
    - Scale slider 0.8..1.2, nearest 0.05.
    - Alpha slider 0..1, nearest 0.1, controlling startAlpha.
    - Path reveal switch.
    - Loop switch.
    - Loop mode segmented control only when loop is true.
- For Loading state, available styles are only SequentialAppear and SequentialAlphaPulse.
- For non-loading states, hide loading-only sequential styles.
- For sequential styles, easing options:
  - SequentialAppear can expose transition easing options.
  - SequentialAlphaPulse should expose only Linear if shown.
- Mark button saves current state into an in-session approvedSpecs map and switches to Handoff.

Handoff mode requirements:
- Handoff panel reuses the preview panel.
- Show summary:
  - State
  - Asset file
  - Preset
  - Duration
  - Loop
  - Vector paths count
  - Trace paths count
  - Whether this spec was approved/marked or is draft.
- Show validation messages.
- Show selectable monospace text blocks for:
  - motion-spec.json
  - Compose handoff
  - Implementation reference

Motion handoff contract:
Create PaymentMotionSpec.kt with:
- enum MotionPreset wire names:
  - Static "static"
  - Trace "trace"
  - Fade "fade"
  - Pop "pop"
  - Pulse "pulse"
  - SequentialAppear "sequentialAppear"
  - SequentialPulse "sequentialPulse"
- enum MotionEasing wire names:
  - Pos "pos"
  - Emphasized "emphasized"
  - Linear "linear"
- enum MotionLoopMode:
  - Cycle "cycle"
  - Yoyo "yoyo"
- enum ReducedMotionMode:
  - Static "static"
- data class PaymentMotionSpec:
  - state
  - assetId
  - assetFile
  - preset
  - durationMillis
  - delayMillis
  - easing
  - loop
  - loopMode
  - pathReveal
  - startAlpha
  - endAlpha
  - startScale
  - endScale
  - transition nullable MotionTransitionSpec
  - reducedMotion default ReducedMotionSpec(Static)
- from(icon, animationSpec) maps UI animation spec into handoff spec.
- toAnimationSpec maps handoff spec back into preview animation spec.
- validate(icon) warnings/errors:
  - duration < 0 is error.
  - duration > 2000 warns.
  - delay > 500 warns.
  - Trace with no tracePaths is error.
  - Sequential presets with fewer than two vector paths is error.
  - SequentialAppear without transition warns.
  - transition outside 30..240 ms warns.
  - loop on non-loading/non-pulse presets warns.
  - SequentialPulse gets an info note that it uses internal alpha and should stay loading-only.
  - If no errors/warnings, add info: "Spec is ready for developer handoff."
- toJson outputs concise style-aware JSON:
  - Always include state, assetId, assetFile, preset, durationMs, reducedMotion.
  - Include delayMs only when > 0.
  - Include global easing only for Trace/Fade/Pop/Pulse.
  - Include loop for loop-capable presets or when true.
  - Include loopMode only when loop is true.
  - Include pathReveal when true or when preset is Trace.
  - Include alpha only when the preset consumes exported alpha and values are not default 1f -> 1f.
  - Include scale only when the preset consumes exported scale and values are not default 1f -> 1f.
  - Include transition only when present.
- toComposeSnippet returns:
  PaymentStatusIcon(
      state = PaymentStatusState.<State>,
      motion = PaymentStatusMotion.<Preset>,
      modifier = Modifier.size(64.dp),
  )
- toImplementationReference must explain the exact tween/easing declarations and renderer behavior for the selected preset.

Expected implementation reference details:
- Include imports for CubicBezierEasing, LinearEasing, tween.
- POS easing declaration: CubicBezierEasing(0.2f, 0f, 0f, 1f).
- Emphasized easing declaration: CubicBezierEasing(0.16f, 1f, 0.3f, 1f).
- Linear easing declaration: LinearEasing.
- Trace notes mention PathMeasure.getSegment and length-weighted multi-path timing.
- Fade/Pop notes mention Animatable progress, alpha lerp, scale lerp, one final ImageVector instance.
- Pulse notes mention yoyo loop and scale lerp.
- SequentialAppear notes mention one vector path list, moving gap, linear timeline, eased local transition.
- SequentialPulse notes mention one vector path list, all paths visible, alpha emphasis through path order.

Motion design guidance:
- This tool is for design tuning first and developer handoff second.
- Keep controls safe and understandable for designers.
- Avoid flashy motion and full-icon disappearance.
- Do not add spin/rotation loading presets.
- Success should feel definitive: 350..450 ms trace with a short delay.
- Error/failure/decline should appear quickly: 180..240 ms fade or restrained pop.
- Loading should use sequential path behavior, not whole-icon rotation.
- Preserve reduced motion behavior.
- Prefer one SVG/vector instance unless a requirement explicitly says otherwise.

Theme/UI:
- Use a simple Material 3 theme with light/dark support.
- Use 8.dp card radius.
- Keep the app utilitarian and focused.
- Do not add decorative backgrounds, marketing copy, or landing-page content.
- Text must fit inside controls; if segmented buttons become cramped with five states, prefer a clear compact design over oversized text.

Verification:
- After implementation, run:
  env JAVA_HOME='/Applications/Android Studio.app/Contents/jbr/Contents/Home' ./gradlew :app:compileDebugKotlin
- If JAVA_HOME differs on the new laptop, use the local Android Studio JBR path or the system JDK 17 path.
- Also run:
  node tools/import-status-icons.mjs
  then compile again.
- Confirm the app launches and that:
  - Success Trace animates smoothly using centerline trace paths.
  - Error and Failure can trace if tracePaths exist.
  - Decline uses payment-declined.svg and defaults to the same Fade preset as Failure.
  - Loading SequentialAppear uses one SVG only, with a moving gap and eased gap transition.
  - Loading SequentialAlphaPulse keeps all paths visible.
  - Handoff JSON omits unused fields such as alpha when a preset does not consume it.
```

