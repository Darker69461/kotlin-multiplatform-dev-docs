[//]: # (title: What's new in Compose Multiplatform %org.jetbrains.compose-eap%)

Here are the highlights for this EAP feature release:

* [Material 3 Expressive theme](#new-material-3-expressive-theme)
* [Customizable shadows](#customizable-shadows)
* [Parameters for the `@Preview` annotation](#parameters-for-the-preview-annotation)
* [Frame rate configuration on iOS](#frame-rate-configuration)
* [Accessibility support on web targets](#accessibility-support)
* [New API for embedding HTML content](#new-api-for-embedding-html-content)

See the full list of changes for this release on [GitHub](https://github.com/JetBrains/compose-multiplatform/releases/tag/v1.9.0-beta01).

## Dependencies

* Gradle Plugin `org.jetbrains.compose`, version %org.jetbrains.compose-eap%. Based on Jetpack Compose libraries:
   * [Runtime 1.9.0-beta02](https://developer.android.com/jetpack/androidx/releases/compose-runtime#1.9.0-beta02)
   * [UI 1.9.0-beta02](https://developer.android.com/jetpack/androidx/releases/compose-ui#1.9.0-beta02)
   * [Foundation 1.9.0-beta02](https://developer.android.com/jetpack/androidx/releases/compose-foundation#1.9.0-beta02)
   * [Material 1.9.0-beta02](https://developer.android.com/jetpack/androidx/releases/compose-material#1.9.0-beta02)
   * [Material3 1.3.2](https://developer.android.com/jetpack/androidx/releases/compose-material3#1.3.2)
* Compose Material3 library `org.jetbrains.compose.material3:1.9.0-alpha04`. Based on [Jetpack Material3 1.4.0-alpha17](https://developer.android.com/jetpack/androidx/releases/compose-material3#1.4.0-alpha17)

  The stable version of the common Material3 library is based on Jetpack Compose Material3 1.3.2, but thanks to
  [decoupled versions](#decoupled-material3-versioning) of Compose Multiplatform and Material3 you can choose a newer EAP version for your project.
* Compose Material3 Adaptive libraries `org.jetbrains.compose.material3.adaptive:adaptive*:1.2.0-alpha04`. Based on [Jetpack Material3 Adaptive 1.2.0-alpha08](https://developer.android.com/jetpack/androidx/releases/compose-material3-adaptive#1.2.0-alpha08)
* Graphics-Shapes library `org.jetbrains.androidx.graphics:graphics-shapes:1.0.0-alpha09`. Based on [Jetpack Graphics-Shapes 1.0.1](https://developer.android.com/jetpack/androidx/releases/graphics#graphics-shapes-1.0.1)
* Lifecycle libraries `org.jetbrains.androidx.lifecycle:lifecycle-*:2.9.1`. Based on [Jetpack Lifecycle 2.9.1](https://developer.android.com/jetpack/androidx/releases/lifecycle#2.9.1)
* Navigation libraries `org.jetbrains.androidx.navigation:navigation-*:2.9.0-beta04`. Based on [Jetpack Navigation 2.9.1](https://developer.android.com/jetpack/androidx/releases/navigation#2.9.1)
* Savedstate library `org.jetbrains.androidx.savedstate:savedstate:1.3.1`. Based on [Jetpack Savedstate 1.3.0](https://developer.android.com/jetpack/androidx/releases/savedstate#1.3.0)
* WindowManager Core library `org.jetbrains.androidx.window:window-core:1.4.0-alpha09`. Based on [Jetpack WindowManager 1.4.0](https://developer.android.com/jetpack/androidx/releases/window#1.4.0)

## Across platforms

### New Material 3 Expressive theme
<secondary-label ref="Experimental"/>

Compose Multiplatform now supports an experimental [`MaterialExpressiveTheme`](https://developer.android.com/reference/kotlin/androidx/compose/material3/package-summary?hl=en#MaterialExpressiveTheme(androidx.compose.material3.ColorScheme,androidx.compose.material3.MotionScheme,androidx.compose.material3.Shapes,androidx.compose.material3.Typography,kotlin.Function0)) 
from the Material 3 library. The expressive theming allows you to customize your Material Design app 
for a more personalized experience.

To use the Expressive theme:

1. Include the latest version of Material 3:
 
    ```kotlin
    implementation("org.jetbrains.compose.material3:material3:%org.jetbrains.compose.material3%")
    ```

2. Use the `MaterialExpressiveTheme()` function with the `@OptIn(ExperimentalMaterial3ExpressiveApi::class)` opt-in to 
configure the overall theme of your UI elements by setting the `colorScheme`, `motionScheme`, `shapes`, and `typography` parameters.

Material components, such as [`Button()`](https://kotlinlang.org/api/compose-multiplatform/material3/androidx.compose.material3/-button.html) 
and [`Checkbox()`](https://kotlinlang.org/api/compose-multiplatform/material3/androidx.compose.material3/-checkbox.html), 
will then automatically use the values you’ve provided.

<img src="compose_expressive_theme.animated.gif" alt="Material 3 Expressive" width="250" preview-src="compose_expressive_theme.png"/>

### Customizable shadows

In Compose Multiplatform %org.jetbrains.compose-eap%, we’ve introduced customizable shadows, adopting Jetpack Compose’s new 
shadow primitives and APIs. In addition to the previously supported `shadow` modifier, you can now use the new API 
to create more advanced and flexible shadow effects.

Two new primitives are available for creating different types of shadows:
`DropShadowPainter()` and `InnerShadowPainter()`.

To apply these new shadows to UI components, configure shadow effects with the `dropShadow` or `innerShadow` modifiers:

<list columns="2">
   <li><code-block lang="kotlin">
        Box(
            Modifier.size(120.dp)
                .dropShadow(
                    RectangleShape,
                    DropShadow(12.dp)
                )
                .background(Color.White)
        )
        Box(
            Modifier.size(120.dp)
                .innerShadow(
                    RectangleShape,
                    InnerShadow(12.dp)
                )
        )
   </code-block></li>
   <li><img src="compose-advanced-shadows.png" type="inline" alt="Customizable shadows" width="200"/></li>
</list>

You can draw shadows of any shape and color, or even use shadow geometry as a mask to create an inner gradient-filled shadow:

<img src="compose-expressive-shadows.png" alt="Expressive shadows" width="244"/>

For details, see the [shadow API reference](https://developer.android.com/reference/kotlin/androidx/compose/ui/graphics/shadow/package-summary.html).

### Parameters for the `@Preview` annotation

The `@Preview` annotation in Compose Multiplatform now includes additional parameters for
configuring how a `@Composable` function is rendered in design-time previews:

* `name`: The display name of the preview.
* `group`: The group name for the preview, enabling the logical organization and selective display of related previews.
* `widthDp`: The maximum width (in dp).
* `heightDp`: The maximum height (in dp).
* `locale`: The current locale of the application.
* `showBackground`: A flag to apply the default background color to the preview.
* `backgroundColor`: A 32-bit ARGB color integer defining the preview’s background color.

These new preview parameters are recognized and work in both IntelliJ IDEA and Android Studio.

### Multiplatform targets in `androidx.compose.runtime:runtime`

To improve Compose Multiplatform's alignment with Jetpack Compose, we've added support for all targets 
directly into the `androidx.compose.runtime:runtime` artifact.

The `org.jetbrains.compose.runtime:runtime` artifact remains fully compatible and now serves as an alias.

### `runComposeUiTest()` with `suspend` lambda

The `runComposeUiTest()` function now accepts a `suspend` lambda, allowing you to use suspending functions 
such as `awaitIdle()`.

The new API guarantees correct test execution on all supported platforms, including proper asynchronous 
handling for web environments:

* For JVM and native targets, `runComposeUiTest()` functions similarly to `runBlocking()`, but skips delays.
* For web targets (Wasm and JS), it returns a `Promise` and executes the test body with delays skipped.

## iOS

### Frame rate configuration

Compose Multiplatform for iOS now supports configuring the preferred frame rate for rendering a composable. 
If an animation is stuttering, you may want to increase the frame rate. On the other hand, if the animation is 
slow or static, you may prefer running it at a lower frame rate to reduce power consumption.

You can set the preferred frame rate category as follows:

```kotlin
Modifier.preferredFrameRate(FrameRateCategory.High)
```

Alternatively, if you need a specific frame rate for a composable, you can define the preferred frame 
rate in frames per second using a non-negative number:

```kotlin
Modifier.preferredFrameRate(30f)
```

## Web

### Accessibility support

Compose Multiplatform now provides initial accessibility support for the web target. This version enables 
screen readers to access description labels and allows users to navigate and click buttons in 
accessible navigation mode.

In this version, the following features are not yet supported:

* Accessibility for interop and container views with scrolls and sliders.
* Traversal indexes.

You can define a component's [semantic properties](compose-accessibility.md#semantic-properties) 
to provide accessibility services with various details, such as the component’s textual description, 
functional type, current state, or unique identifier.

For example, by setting `Modifier.semantics { heading() }` on a composable, you inform accessibility 
services that this element acts as a heading, similar to a chapter or a subsection title in a document. 
Screen readers can then use this information for content navigation, allowing users to jump directly between headings.

```kotlin
Text(
    text = "This is heading", 
    modifier = Modifier.semantics { heading() }
)
```

Accessibility support is now enabled by default, but you can disable it at any time by adjusting `isA11YEnabled`:

```kotlin
ComposeViewport(
    viewportContainer = document.body!!,
    configure = { isA11YEnabled = false }
) {
    Text("Hello, Compose Multiplatform for web")
}
```

### New API for embedding HTML content

With the new `WebElementView()` composable function, you can seamlessly integrate HTML elements 
into your web application.

The embedded HTML element overlays the canvas area based on the size defined in your Compose code. It intercepts 
input events within that area, preventing those events from being received by Compose Multiplatform.

Here’s an example of `WebElementView()` being used to create and embed an HTML element that displays 
an interactive map view within your Compose application:

```kotlin
private val ttOSM =
    "https://www.openstreetmap.org/export/embed.html?bbox=4.890965223312379%2C52.33722052818563%2C4.893990755081177%2C52.33860862450587&amp;layer=mapnik"

@Composable
fun Map() {
    Box(
        modifier = Modifier.fillMaxWidth().fillMaxHeight()
    ) {
        WebElementView(
            factory = {
                (document.createElement("iframe")
                        as HTMLIFrameElement)
                    .apply { src = ttOSM }
            },
            modifier = Modifier.fillMaxSize(),
            update = { iframe -> iframe.src = iframe.src }
        )
    }
}
```

Note that you can only use this function with the `ComposeViewport` entry point, as `CanvasBasedWindow` is deprecated.

### Context menus

Compose Multiplatform %org.jetbrains.compose-eap% brings the following updates for web context menus:

* Text context menu: The standard Compose text context menu is now fully supported for both mobile and desktop modes.
* New customizable context menu: We’ve adopted Jetpack Compose’s new API for custom web context menus.
   For now, it’s available in desktop mode only.

   To enable this new API, use the following setting in the application entry point:
   
   ```kotlin
   ComposeFoundationFlags.isNewContextMenuEnabled = true
   ```

### Simplified API for binding to the navigation graph

Compose Multiplatform has introduced a new API for binding the browser’s navigation state to the `NavController`:

```kotlin
suspend fun NavController.bindToBrowserNavigation()
```

The new function removes the need to interact directly with the `window` API, 
simplifying both the Kotlin/Wasm and Kotlin/JS source sets.

The previously used `Window.bindToNavigation()` function has been deprecated in favor of 
the new `NavController.bindToBrowserNavigation()` function.

Before:

```kotlin
LaunchedEffect(Unit) {
    // Directly interacts with the window object
    window.bindToNavigation(navController)
}
```

After:

```kotlin
LaunchedEffect(Unit) {
    // Implicitly accesses the window object
    navController.bindToBrowserNavigation()
}
```

## Gradle plugin

### Decoupled Material3 versioning

The versions and stability levels of the Material3 library and Compose Multiplatform Gradle plugin no 
longer have to be aligned. The `compose.material3` DSL alias now references Material3 1.8.2 from the previous 
stable release of Jetpack Compose.

If you want to use a newer Material3 version with Expressive design support, replace the Material 3 dependency 
in `build.gradle.kts` with the following:

```kotlin
implementation("org.jetbrains.compose.material3:material3:%org.jetbrains.compose.material3%")
```