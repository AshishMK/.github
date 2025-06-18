

## Quick Overview of Tools and Libraries I Use for Robust and Stable App Development

**@see [JetUpdates App](https://github.com/AshishMK/JetUpdates) code for the implementation of tools and practices defined below.

**Code Maintenance / Optimization Tools:**
- **Lint**: A tool that helps identify and correct issues related to the structural quality of your code. It can detect problems such as unused namespaces in XML files, API calls not supported by the target API versions (e.g., using a custom button instead of a base button), and deprecated elements.
- **Spotless:** A code formatting tool that automatically fixes common formatting issues such as spacing, indentation, new lines, and unnecessary imports. You can also define custom formatting rules to be applied by Spotless.

**Performance Tools:**
- **Layout Inspector:** A debugging tool that helps detect unnecessary recompositions by showing recomposition counts for UI elements (i.e., Composables) currently displayed on the device’s screen while the user is interacting with it (e.g., scrolling a list).
- **Compose Compiler Reports**: Used to debug if a composable is recomposing unnecessarily due to unstable parameters or classes. The compiler outputs reports showing whether functions and classes used in composables are stable (i.e., skippable or restartable).
- **ReportFullyDrawn API:** Signals to Android that the app is fully ready for use. This allows Android to optimize future startups by preloading I/O operations.
- **Benchmark:** Similar to UI tests, benchmarks are used to detect and fix performance issues in code. They help identify bottlenecks during startup and important user interactions, such as long scrolling lists or screen transitions.
- **Baseline Profiles**: Improve code execution speed (~30%) from the first launch by avoiding interpretation and JIT. These are shipped with the APK/App Bundle as a list of classes and methods compiled at install time. When used alongside benchmarks, they can be generated for the same use cases (e.g., startup profiles).
- **Profiling:** Helps determine how busy the main thread is, even when the app appears idle. You can record CPU traces and analyze them using Systrace or Perfetto. The memory profiler offers a live view of object allocations and garbage collections.
- **JankStats:** A library used to capture janky frames along with metadata that explains how the app reached the state where the jank occurred. This information can be sent to a backend for analysis.
- **Tracing:** The Tracing API allows you to label specific sections of code using the `trace` method, which can then be observed in tools like the memory profiler for better insight into runtime performance.

**Safe Guards:**
- **DependencyGuard**: Ensures safe version updates for dependencies and prevents unintended or experimental updates.
- **Badging:** Helps guard against unintended changes in the manifest or resource files.

**CI/CD:**

> CI (Continuous Integration) is a practice where developers frequently
> merge code into a central repository, triggering automated builds and
> tests. CD (Continuous Delivery) enables automatic deployments after CI
> passes.

We use GitHub Actions as our CI/CD system. Two workflows are defined:
1. For validating changes (tests, checks on commits).
2. For releasing stable, production-ready builds.

- **Dependabot / Renovate:** These tools scan project dependencies and open issues or pull requests when updates are available. We use Renovate for its ease of configuration and shareability across repositories.

**Design Architecture:**
- **Material 3:** A new design language based on the Material Design system, featuring dynamic theming (“Material You”). It’s built on three pillars: Typography, Color, and Shape.
- **Canonical Layouts:** Provide built-in support for responsive layouts across different screen sizes without needing separate code for each. Examples include Feed Layout, List-Detail Layout, and Supporting Layout.
- **Coil:** Used for efficient dynamic image loading.

**Code Architecture:**
- **Uni-directional Data Flow:** State flows downward from the data layer to the UI, while events flow upward from the UI to the data layer.
- **Layered Architecture:** 
  - UI layer → (optional) Domain layer → Data layer.
  - State holders implemented via ViewModels and dedicated StateHolder classes.
  - Reactive design using StateFlow and side-effect APIs.
- **Modularization:**
  - The app is split into modular components: App, Feature, and Core.
    - App module can depend on Feature and Core.
    - Feature modules handle single responsibilities and cannot depend on each other or the App module.
    - Core modules provide shared utility code and cannot depend on Feature or App modules.
- **Convention Plugins:** Prevent duplication of dependencies across many modules by defining them centrally.

**Test-Driven Development (TDD):**
Emphasizes writing tests to ensure the app is robust, scalable, and maintainable.
- Frameworks used: Robolectric, JUnit, ComposeTest, Espresso
- Test types:
  - Unit Tests
  - Screenshot Tests
  - Instrumentation Tests (UI Tests)
