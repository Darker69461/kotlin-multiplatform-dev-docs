[//]: # (title: Multiplatform Gradle DSL reference)

The Kotlin Multiplatform Gradle plugin is a tool for creating Kotlin Multiplatform projects.
Here we provide a reference of its contents; use it as a reminder when writing Gradle build scripts
for Kotlin Multiplatform projects. Learn the [concepts of Kotlin Multiplatform projects, how to create and configure them](multiplatform-discover-project.md).

## ID and version

The fully qualified name of the Kotlin Multiplatform Gradle plugin is `org.jetbrains.kotlin.multiplatform`. 
If you use the Kotlin Gradle DSL, you can apply the plugin with `kotlin("multiplatform")`.
The plugin versions match the Kotlin release versions. The most recent version is %kotlinVersion%.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
plugins {
    kotlin("multiplatform") version "%kotlinVersion%"
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
plugins {
    id 'org.jetbrains.kotlin.multiplatform' version '%kotlinVersion%'
}
```

</tab>
</tabs>

## Top-level blocks

`kotlin {}` is the top-level block for multiplatform project configuration in the Gradle build script.
Inside `kotlin {}`, you can write the following blocks:

| **Block**            | **Description**                                                                                                                          |
|----------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| _&lt;targetName&gt;_ | Declares a particular target of a project. The names of available targets are listed in the [Targets](#targets) section.                 |
| `targets`            | Lists all targets of the project.                                                                                                        |
| `sourceSets`         | Configures predefined and declares custom [source sets](#source-sets) of the project.                                                    |
| `compilerOptions`    | Specifies common extension-level [compiler options](#compiler-options) that are used as defaults for all targets and shared source sets. |

## Targets

A _target_ is a part of the build responsible for compiling, testing, and packaging a piece of software aimed at
one of the supported platforms. Kotlin provides targets for each platform, so you can instruct Kotlin to compile code for
that specific target. Learn more about [setting up targets](multiplatform-discover-project.md#targets).

Each target can have one or more [compilations](#compilations). In addition to default compilations for
test and production purposes, you can [create custom compilations](multiplatform-configure-compilations.md#create-a-custom-compilation).

The targets of a multiplatform project are described in the corresponding blocks inside `kotlin {}`, for example, `jvm`, `androidTarget`, `iosArm64`.
The complete list of available targets is the following:

<table>
    <tr>
        <th>Target platform</th>
        <th>Target</th>
        <th>Comments</th>
    </tr>
    <tr>
        <td>Kotlin/JVM</td>
        <td><code>jvm</code></td>
        <td></td>
    </tr>
    <tr>
        <td rowspan="2">Kotlin/Wasm</td>
        <td><code>wasmJs</code></td>
        <td>Use it if you plan to run your projects in the JavaScript runtime.</td>
    </tr>
    <tr>
        <td><code>wasmWasi</code></td>
        <td>Use it if you need support for the <a href="https://github.com/WebAssembly/WASI">WASI</a> system interface.</td>
    </tr>
    <tr>
        <td>Kotlin/JS</td>
        <td><code>js</code></td>
        <td>
            <p>Select the execution environment:</p>
            <list>
                <li><code>browser {}</code> for applications running in the browser.</li>
                <li><code>nodejs {}</code> for applications running on Node.js.</li>
            </list>
            <p>Learn more in <a href="https://kotlinlang.org/docs/js-project-setup.html#execution-environments">Setting up a Kotlin/JS project</a>.</p>
        </td>
    </tr>
    <tr>
        <td>Kotlin/Native</td>
        <td></td>
        <td>
            <p>Learn about currently supported targets for the macOS, Linux, and Windows hosts in <a href="https://kotlinlang.org/docs/native-target-support.html">Kotlin/Native target support</a>.</p>
        </td>
    </tr>
    <tr>
        <td>Android applications and libraries</td>
        <td><code>androidTarget</code></td>
        <td>
            <p>Manually apply an Android Gradle plugin: <code>com.android.application</code> or <code>com.android.library</code>.</p>
            <p>You can only create one Android target per Gradle subproject.</p>
        </td>
    </tr>
</table>

> A target that is not supported by the current host is ignored during building and, therefore, not published.
>
{style="note"}

```groovy
kotlin {
    jvm()
    iosArm64()
    macosX64()
    js().browser()
}
```

The configuration of a target can include two parts:

* [Common configuration](#common-target-configuration) available for all targets.
* Target-specific configuration.

Each target can have one or more [compilations](#compilations).

### Common target configuration

In any target block, you can use the following declarations:

| **Name**            | **Description**                                                                                                                                                                            | 
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `platformType`      | The Kotlin platform for this target. Available values: `jvm`, `androidJvm`, `js`, `wasm`, `native`, `common`.                                                                              |
| `artifactsTaskName` | The name of the task that builds the resulting artifacts of this target.                                                                                                                   |
| `components`        | Components used to set up Gradle publications.                                                                                                                                             |
| `compilerOptions`   | [Compiler options](#compiler-options) used for the target. This declaration overrides any `compilerOptions {}` configured at [top level](multiplatform-dsl-reference.md#top-level-blocks). |

### Web targets

The `js {}` block describes the configuration of Kotlin/JS targets, and the `wasmJs {}` block describes the configuration of
Kotlin/Wasm targets interoperable with JavaScript. They can contain one of two blocks depending on the target execution
environment:

| **Name**              | **Description**                      | 
|-----------------------|--------------------------------------|
| [`browser`](#browser) | Configuration of the browser target. |
| [`nodejs`](#node-js)  | Configuration of the Node.js target. |

Learn more about [configuring Kotlin/JS projects](https://kotlinlang.org/docs/js-project-setup.html).

A separate `wasmWasi {}` block describes the configuration of Kotlin/Wasm targets that support the WASI system interface.
Here, only the [`nodejs`](#node-js) execution environment is available:

```kotlin
kotlin {
    wasmWasi {
        nodejs()
        binaries.executable()
    }
}
```

All the web targets, `js`, `wasmJs`, and `wasmWasi`, also support the `binaries.executable()` call. It explicitly
instructs the Kotlin compiler to emit executable files. For more information, see [Execution environments](https://kotlinlang.org/docs/js-project-setup.html#execution-environments)
in the Kotlin/JS documentation.

#### Browser

`browser {}` can contain the following configuration blocks:

| **Name**       | **Description**                                                            | 
|----------------|----------------------------------------------------------------------------|
| `testRuns`     | Configuration of test execution.                                           |
| `runTask`      | Configuration of project running.                                          |
| `webpackTask`  | Configuration of project bundling with [Webpack](https://webpack.js.org/). |
| `distribution` | Path to output files.                                                      |

```kotlin
kotlin {
    js().browser {
        webpackTask { /* ... */ }
        testRuns { /* ... */ }
        distribution {
            directory = File("$projectDir/customdir/")
        }
    }
}
```

#### Node.js

`nodejs {}` can contain configurations of test and run tasks:

| **Name**   | **Description**                   | 
|------------|-----------------------------------|
| `testRuns` | Configuration of test execution.  |
| `runTask`  | Configuration of project running. |

```kotlin
kotlin {
    js().nodejs {
        runTask { /* ... */ }
        testRuns { /* ... */ }
    }
}
```

### Native targets

For native targets, the following specific blocks are available:

| **Name**    | **Description**                                          | 
|-------------|----------------------------------------------------------|
| `binaries`  | Configuration of [binaries](#binaries) to produce.       |
| `cinterops` | Configuration of [interop with C libraries](#cinterops). |

#### Binaries

There are the following kinds of binaries:

| **Name**     | **Description**        | 
|--------------|------------------------|
| `executable` | Product executable.    |
| `test`       | Test executable.       |
| `sharedLib`  | Shared library.        |
| `staticLib`  | Static library.        |
| `framework`  | Objective-C framework. |

```kotlin
kotlin {
    linuxX64 { // Use your target instead.
        binaries {
            executable {
                // Binary configuration.
            }
        }
    }
}
```

For binary configuration, the following parameters are available:

| **Name**      | **Description**                                                                                                                                                   | 
|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `compilation` | The compilation from which the binary is built. By default, `test` binaries are based on the `test` compilation while other binaries - on the `main` compilation. |
| `linkerOpts`  | Options passed to a system linker during binary building.                                                                                                         |
| `baseName`    | Custom base name for the output file. The final file name will be formed by adding system-dependent prefix and postfix to this base name.                         |
| `entryPoint`  | The entry point function for executable binaries. By default, it's `main()` in the root package.                                                                  |
| `outputFile`  | Access to the output file.                                                                                                                                        |
| `linkTask`    | Access to the link task.                                                                                                                                          |
| `runTask`     | Access to the run task for executable binaries. For targets other than `linuxX64`, `macosX64`, or `mingwX64` the value is `null`.                                 |
| `isStatic`    | For Objective-C frameworks. Includes a static library instead of a dynamic one.                                                                                   |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
binaries {
    executable("my_executable", listOf(RELEASE)) {
        // Build a binary on the basis of the test compilation.
        compilation = compilations["test"]

        // Custom command line options for the linker.
        linkerOpts = mutableListOf("-L/lib/search/path", "-L/another/search/path", "-lmylib")

        // Base name for the output file.
        baseName = "foo"

        // Custom entry point function.
        entryPoint = "org.example.main"

        // Accessing the output file.
        println("Executable path: ${outputFile.absolutePath}")

        // Accessing the link task.
        linkTask.dependsOn(additionalPreprocessingTask)

        // Accessing the run task.
        // Note that the runTask is null for non-host platforms.
        runTask?.dependsOn(prepareForRun)
    }

    framework("my_framework" listOf(RELEASE)) {
        // Include a static library instead of a dynamic one into the framework.
        isStatic = true
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
binaries {
    executable('my_executable', [RELEASE]) {
        // Build a binary on the basis of the test compilation.
        compilation = compilations.test

        // Custom command line options for the linker.
        linkerOpts = ['-L/lib/search/path', '-L/another/search/path', '-lmylib']

        // Base name for the output file.
        baseName = 'foo'

        // Custom entry point function.
        entryPoint = 'org.example.main'

        // Accessing the output file.
        println("Executable path: ${outputFile.absolutePath}")

        // Accessing the link task.
        linkTask.dependsOn(additionalPreprocessingTask)

        // Accessing the run task.
        // Note that the runTask is null for non-host platforms.
        runTask?.dependsOn(prepareForRun)
    }

    framework('my_framework' [RELEASE]) {
        // Include a static library instead of a dynamic one into the framework.
        isStatic = true
    }
}
```

</tab>
</tabs>

Learn more about [building native binaries](multiplatform-build-native-binaries.md).

#### Cinterops

`cinterops` is a collection of descriptions for interop with native libraries.
To provide interop with a library, add an entry to `cinterops` and define its parameters:

| **Name**         | **Description**                                       | 
|------------------|-------------------------------------------------------|
| `definitionFile` | The `.def` file describing the native API.            |
| `packageName`    | Package prefix for the generated Kotlin API.          |
| `compilerOpts`   | Options to pass to the compiler by the cinterop tool. |
| `includeDirs`    | Directories to look for headers.                      |
| `header`         | Header to be included in the bindings.                |
| `headers`        | The list of headers to be included in the bindings.   |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    linuxX64 { // Replace with a target you need.
        compilations.getByName("main") {
            val myInterop by cinterops.creating {
                // Definition file describing the native API.
                // The default path is src/nativeInterop/cinterop/<interop-name>.def
                definitionFile.set(project.file("def-file.def"))

                // Package to place the Kotlin API generated.
                packageName("org.sample")

                // Options to be passed to compiler by cinterop tool.
                compilerOpts("-Ipath/to/headers")

                // Directories for header search (an analogue of the -I<path> compiler option).
                includeDirs.allHeaders("path1", "path2")

                // A shortcut for includeDirs.allHeaders.
                includeDirs("include/directory", "another/directory")

                // Header files to be included in the bindings.
                header("path/to/header.h")
                headers("path/to/header1.h", "path/to/header2.h")
            }

            val anotherInterop by cinterops.creating { /* ... */ }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    linuxX64 { // Replace with a target you need.
        compilations.main {
            cinterops {
                myInterop {
                    // Definition file describing the native API.
                    // The default path is src/nativeInterop/cinterop/<interop-name>.def
                    definitionFile = project.file("def-file.def")

                    // Package to place the Kotlin API generated.
                    packageName 'org.sample'

                    // Options to be passed to compiler by cinterop tool.
                    compilerOpts '-Ipath/to/headers'

                    // Directories for header search (an analogue of the -I<path> compiler option).
                    includeDirs.allHeaders("path1", "path2")

                    // A shortcut for includeDirs.allHeaders.
                    includeDirs("include/directory", "another/directory")

                    // Header files to be included in the bindings.
                    header("path/to/header.h")
                    headers("path/to/header1.h", "path/to/header2.h")
                }

                anotherInterop { /* ... */ }
            }
        }
    }
}
```

</tab>
</tabs>

For more cinterop properties, see [Definition file](https://kotlinlang.org/docs/native-definition-file.html#properties).

### Android targets

The Kotlin Multiplatform plugin has a specific function that helps you configure [build variants](https://developer.android.com/studio/build/build-variants)
for the Android target:

| **Name**                      | **Description**                                                                                                                                      | 
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|
| `publishLibraryVariants()`    | Specifies build variants to publish. Learn more about [publishing Android libraries](multiplatform-publish-lib-setup.md#publish-an-android-library). |

```kotlin
kotlin {
    androidTarget {
        publishLibraryVariants("release")
    }
}
```

Learn more about [compilation for Android](multiplatform-configure-compilations.md#compilation-for-android).

> The `androidTarget` configuration inside the `kotlin {}` block doesn't replace the build configuration of any Android project.
> Learn more about writing build scripts for Android projects in [Android developer documentation](https://developer.android.com/studio/build).
>
{style="note"}

## Source sets

The `sourceSets {}` block describes source sets of the project. A source set contains Kotlin source files that participate
in compilations together, along with their resources and dependencies. 

A multiplatform project contains [predefined](#predefined-source-sets) source sets for its targets;
developers can also create [custom](#custom-source-sets) source sets for their needs.

### Predefined source sets

Predefined source sets are set up automatically upon creation of a multiplatform project.
Available predefined source sets are the following:

| **Name**                                    | **Description**                                                                                                                                                                                               | 
|---------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `commonMain`                                | Code and resources shared between all platforms. Available in all multiplatform projects. Used in all main [compilations](#compilations) of a project.                                                        |
| `commonTest`                                | Test code and resources shared between all platforms. Available in all multiplatform projects. Used in all test compilations of a project.                                                                    |
| _&lt;targetName&gt;&lt;compilationName&gt;_ | Target-specific sources for a compilation. _&lt;targetName&gt;_ is the name of a predefined target and _&lt;compilationName&gt;_ is the name of a compilation for this target. Examples: `jsTest`, `jvmMain`. |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    //...
    sourceSets {
        commonMain { /* ... */ }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    //...
    sourceSets { 
        commonMain { /* ... */ }
    }
}
```

</tab>
</tabs>

Learn more about [source sets](multiplatform-discover-project.md#source-sets).

### Custom source sets

Custom source sets are created by the project developers manually.
To create a custom source set, add a section with its name inside the `sourceSets` section.
If using Kotlin Gradle DSL, mark custom source sets `by creating`.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    //...
    sourceSets { 
        val myMain by creating { /* ... */ } // create a new source set by the name 'MyMain'
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    //...
    sourceSets { 
        myMain { /* ... */ } // create or configure a source set by the name 'myMain' 
    }
}
```

</tab>
</tabs>

Note that a newly created source set isn't connected to other ones. To use it in the project's compilations,
[connect it with other source sets](multiplatform-hierarchy.md#manual-configuration).

### Source set parameters

Configurations of source sets are stored inside the corresponding blocks of `sourceSets {}`. A source set has the following parameters:

| **Name**           | **Description**                                                                        | 
|--------------------|----------------------------------------------------------------------------------------|
| `kotlin.srcDir`    | Location of Kotlin source files inside the source set directory.                       |
| `resources.srcDir` | Location of resources inside the source set directory.                                 |
| `dependsOn`        | [Connection with another source set](multiplatform-hierarchy.md#manual-configuration). |
| `dependencies`     | [Dependencies](#dependencies) of the source set.                                       |
| `languageSettings` | [Language settings](#language-settings) applied to the shared source set.              |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin { 
    //...
    sourceSets { 
        commonMain {
            kotlin.srcDir("src")
            resources.srcDir("res")

            dependencies {
                /* ... */
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin { 
    //...
    sourceSets { 
        commonMain {
            kotlin.srcDir('src')
            resources.srcDir('res')

            dependencies {
                /* ... */
            }
        }
    }
}
``` 

</tab>
</tabs>

## Compilations

A target can have one or more compilations, for example, for production or testing. There are [predefined compilations](#predefined-compilations)
that are added automatically upon target creation. You can additionally create [custom compilations](#custom-compilations).

To refer to all or some particular compilations of a target, use the `compilations` object collection.
From `compilations`, you can refer to a compilation by its name.

Learn more about [configuring compilations](multiplatform-configure-compilations.md).

### Predefined compilations

Predefined compilations are created automatically for each target of a project except for Android targets.
Available predefined compilations are the following:

| **Name** | **Description**                     | 
|----------|-------------------------------------|
| `main`   | Compilation for production sources. |
| `test`   | Compilation for tests.              |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            output // get the main compilation output
        }

        compilations["test"].runtimeDependencyFiles // get the test runtime classpath
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilations.main.output // get the main compilation output
        compilations.test.runtimeDependencyFiles // get the test runtime classpath
    }
}
```

</tab>
</tabs>

### Custom compilations

In addition to predefined compilations, you can create your own custom compilations.
To do that, set up an [`associateWith`](https://kotlinlang.org/docs/gradle-configure-project.html#associate-compiler-tasks) relation between the new
and the `main` compilation. If you're using the Kotlin Gradle DSL, mark custom compilations with `by creating`:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        compilations {
            val main by getting
            val integrationTest by creating {
                // Import main and its classpath as dependencies and establish internal visibility
                associateWith(main)
                defaultSourceSet {
                    dependencies {
                        implementation(kotlin("test-junit"))
                        /* ... */
                    }
                }

                // Create a test task to run the tests produced by this compilation
                testRuns.create("integration") {
                    // Configure the test task
                    setExecutionSourceFrom(integrationTest)
                }
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilations.create('integrationTest') {
            def main = compilations.main
            // Import main and its classpath as dependencies and establish internal visibility
            associateWith(main)
            defaultSourceSet {
                dependencies {
                    implementation kotlin('test-junit')
                    /* ... */
                }
            }

            // Create a test task to run the tests produced by this compilation
            testRuns.create('integration') {
                // Configure the test task
                setExecutionSourceFrom(compilations.integrationTest)
            }
        }
    }
}
```

</tab>
</tabs>

By associating compilations, you add the main compilation output as a dependency and establish the `internal` visibility
between compilations.

Learn more about creating [custom compilations](multiplatform-configure-compilations.md#create-a-custom-compilation).

### Compilation parameters

A compilation has the following parameters:

| **Name**                 | **Description**                                                                                                                                                           | 
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `defaultSourceSet`       | The compilation's default source set.                                                                                                                                     |
| `kotlinSourceSets`       | Source sets participating in the compilation.                                                                                                                             |
| `allKotlinSourceSets`    | Source sets participating in the compilation and their connections via `dependsOn()`.                                                                                     |
| `compilerOptions`        | Compiler options applied to the compilation. For the list of available options, see [Compiler options](https://kotlinlang.org/docs/gradle-compiler-options.html).         |
| `compileKotlinTask`      | Gradle task for compiling Kotlin sources.                                                                                                                                 |
| `compileKotlinTaskName`  | Name of `compileKotlinTask`.                                                                                                                                              |
| `compileAllTaskName`     | Name of the Gradle task for compiling all sources of a compilation.                                                                                                       |
| `output`                 | The compilation output.                                                                                                                                                   |
| `compileDependencyFiles` | Compile-time dependency files (classpath) of the compilation. For all Kotlin/Native compilations, this automatically includes standard library and platform dependencies. |
| `runtimeDependencyFiles` | Runtime dependency files (classpath) of the compilation.                                                                                                                  |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        val main by compilations.getting {
            compileTaskProvider.configure {
                compilerOptions {
                    // Set up the Kotlin compiler options for the 'main' compilation:
                    jvmTarget.set(JvmTarget.JVM_1_8)
                }
            }
        
            compileKotlinTask // get the Kotlin task 'compileKotlinJvm' 
            output // get the main compilation output
        }
        
        compilations["test"].runtimeDependencyFiles // get the test runtime classpath
    }

    // Configure all compilations of all targets:
    compilerOptions {
        allWarningsAsErrors.set(true)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilations.main {
            compileTaskProvider.configure {
                compilerOptions {
                    // Setup the Kotlin compiler options for the 'main' compilation:
                    jvmTarget = JvmTarget.JVM_1_8
                }
            }
        }

        compilations.main.compileKotlinTask // get the Kotlin task 'compileKotlinJvm' 
        compilations.main.output // get the main compilation output
        compilations.test.runtimeDependencyFiles // get the test runtime classpath
    }

    // Configure all compilations of all targets:
    compilerOptions {
        allWarningsAsErrors = true
    }
}
```

</tab>
</tabs>

## Compiler options

You can configure compiler options in your projects at three different levels:

* **Extension level**, in the `kotlin {}` block.
* **Target level**, in a target block.
* **Compilation unit level**, usually in a specific compilation task.

![Kotlin compiler options levels](compiler-options-levels.svg){width=700}

Settings at a higher level work as defaults for the level below:

* Compiler options set at the extension level are the default for target-level options, including shared source sets like `commonMain`, `nativeMain`, and `commonTest`.
* Compiler options set at the target level are the default for options at the compilation unit (task) level, like `compileKotlinJvm` and `compileTestKotlinJvm` tasks.

Configurations made at a lower level override similar settings at higher levels:

* Task-level compiler options override similar settings at the target or extension level.
* Target-level compiler options override similar settings at the extension level.

For the list of possible compiler options, see [All compiler options](https://kotlinlang.org/docs/gradle-compiler-options.html#all-compiler-options).

### Extension level

To configure compiler options for all targets in your project, use the `compilerOptions {}` block at the top level:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    // Configures all compilations of all targets
    compilerOptions {
        allWarningsAsErrors.set(true)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    // Configures all compilations of all targets:
    compilerOptions {
        allWarningsAsErrors = true
    }
}
```

</tab>
</tabs>

### Target level

To configure compiler options for a specific target in your project, use the `compilerOptions {}` block inside the target block:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        // Configures all compilations of the JVM target
        compilerOptions {
            allWarningsAsErrors.set(true)
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        // Configures all compilations of the JVM target
        compilerOptions {
            allWarningsAsErrors = true
        }
    }
}
```

</tab>
</tabs>

### Compilation unit level

To configure compiler options for a specific task, use the `compilerOptions {}` block inside the task:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
task.named<KotlinJvmCompile>("compileKotlinJvm") {
    compilerOptions {
        allWarningsAsErrors.set(true)
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
task.named<KotlinJvmCompile>("compileKotlinJvm") {
    compilerOptions {
        allWarningsAsErrors = true
    }
}
```

</tab>
</tabs>

To configure compiler options for a specific compilation, use the `compilerOptions {}` block within the compilation's task provider:

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    jvm {
        compilations.named(KotlinCompilation.MAIN_COMPILATION_NAME) {
            compileTaskProvider.configure {
                // Configures the 'main' compilation:
                compilerOptions {
                    allWarningsAsErrors.set(true)
                }
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    jvm {
        compilations.named(KotlinCompilation.MAIN_COMPILATION_NAME) {
            compileTaskProvider.configure {
                // Configures the 'main' compilation:
                compilerOptions {
                    allWarningsAsErrors = true
                }
            }
        }
    }
}
```

</tab>
</tabs>

### Migrate from `kotlinOptions {}` to `compilerOptions {}` {collapsible="true"}

Before Kotlin 2.2.0, you could configure compiler options using the `kotlinOptions {}` block. Since the `kotlinOptions {}`
block is deprecated in Kotlin 2.2.0, you need to use the `compilerOptions {}` block in your build scripts instead. 
See [Migrate from `kotlinOptions{}` to `compilerOptions{}`](https://kotlinlang.org/docs/gradle-compiler-options.html#migrate-from-kotlinoptions-to-compileroptions), for more information.

## Dependencies

The `dependencies {}` block of the source set declaration contains the dependencies of this source set.

Learn more about [configuring dependencies](https://kotlinlang.org/docs/gradle-configure-project.html).

There are four types of dependencies:

| **Name**         | **Description**                                                                     | 
|------------------|-------------------------------------------------------------------------------------|
| `api`            | Dependencies used in the API of the current module.                                 |
| `implementation` | Dependencies used in the module but not exposed outside it.                         |
| `compileOnly`    | Dependencies used only for compilation of the current module.                       |
| `runtimeOnly`    | Dependencies available at runtime but not visible during compilation of any module. |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    //...
    sourceSets {
        commonMain {
            dependencies {
                api("com.example:foo-metadata:1.0")
            }
        }
        jvmMain {
            dependencies {
                implementation("com.example:foo-jvm:1.0")
            }
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    //...
    sourceSets {
        commonMain {
            dependencies {
                api 'com.example:foo-metadata:1.0'
            }
        }
        jvmMain {
            dependencies {
                implementation 'com.example:foo-jvm:1.0'
            }
        }
    }
}
```

</tab>
</tabs>

Additionally, source sets can depend on each other and form a hierarchy.
In this case, the [`dependsOn()`](#source-set-parameters) relation is used.

Source set dependencies can also be declared in the top-level `dependencies {}` block of the build script.
In this case, their declarations follow the pattern `<sourceSetName><DependencyKind>`, for example, `commonMainApi`.

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
dependencies {
    "commonMainApi"("com.example:foo-common:1.0")
    "jvm6MainApi"("com.example:foo-jvm6:1.0")
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
dependencies {
    commonMainApi 'com.example:foo-common:1.0'
    jvm6MainApi 'com.example:foo-jvm6:1.0'
}
```

</tab>
</tabs>

## Language settings

The `languageSettings {}` block in a source set defines certain aspects of project analysis and compilation. Use the
`languageSettings {}` block only to configure settings that apply specifically to a shared source set. For all other cases,
use the `compilerOptions {}` block to [configure compiler options](#compiler-options) at the extension or target level.

The following language settings are available:

| **Name**                | **Description**                                                                                                                                                                 | 
|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `languageVersion`       | Provides source compatibility with the specified version of Kotlin.                                                                                                             |
| `apiVersion`            | Allows using declarations only from the specified version of Kotlin bundled libraries.                                                                                          |
| `enableLanguageFeature` | Enables the specified language feature. The available values correspond to the language features that are currently experimental or have been introduced as such at some point. |
| `optIn`                 | Allows using the specified [opt-in annotation](https://kotlinlang.org/docs/opt-in-requirements.html).                                                                           |
| `progressiveMode`       | Enables the [progressive mode](https://kotlinlang.org/docs/whatsnew13.html#progressive-mode).                                                                                   |

<tabs group="build-script">
<tab title="Kotlin" group-key="kotlin">

```kotlin
kotlin {
    sourceSets.all {
        languageSettings.apply {
            languageVersion = "%languageVersion%" // possible values: "1.8", "1.9", "2.0", "2.1"
            apiVersion = "%apiVersion%" // possible values: "1.8", "1.9", "2.0", "2.1"
            enableLanguageFeature("InlineClasses") // language feature name
            optIn("kotlin.ExperimentalUnsignedTypes") // annotation FQ-name
            progressiveMode = true // false by default
        }
    }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
kotlin {
    sourceSets.all {
        languageSettings {
            languageVersion = '%languageVersion%' // possible values: '1.8', '1.9', '2.0', '2.1'
            apiVersion = '%apiVersion%' // possible values: '1.8', '1.9', '2.0', '2.1'
            enableLanguageFeature('InlineClasses') // language feature name
            optIn('kotlin.ExperimentalUnsignedTypes') // annotation FQ-name
            progressiveMode = true // false by default
        }
    }
}
```

</tab>
</tabs>
