# Tools, Architecture & Practices for Production-Grade Android Development

> **Note:** See the [JetUpdates App](https://github.com/AshishMK/JetUpdates) repository for the implementation of the tools, architecture, and engineering practices defined below.

---

## Dependency Injection (DI)

* **Hilt:** Built on Dagger to provide a standardized compile-time dependency injection solution across Android components. Eliminates manual boilerplate through predefined scope annotations (`@Singleton`, `@ViewModelScoped`), standard component lifecycles, and direct integration with Jetpack Compose (`hiltViewModel()`), Navigation, and WorkManager.

---

## Code Architecture & Design

* **Uni-directional Data Flow (UDF):** State flows downward from the data layer to the UI, while events flow upward from the UI to the data layer.
* **Layered Architecture:**
* UI layer → (optional) Domain layer → Data layer.
* State holders implemented via ViewModels and dedicated `StateHolder` classes.
* Reactive design using `StateFlow` and side-effect APIs.


* **Modularization:**
* **App module:** Depends on Feature and Core modules.
* **Feature modules:** Handle isolated business features; cannot depend on each other or the App module.
* **Core modules:** Provide shared utilities, design systems, network engines, and data access; cannot depend on Feature or App modules.


* **Convention Plugins:** Custom Gradle convention plugins prevent duplication of build logic and dependency configurations across multi-module setups.
* **Material 3:** Modern design system incorporating dynamic color ("Material You") built on three pillars: Typography, Color, and Shape.
* **Canonical Layouts:** Responsive layout support (Feed, List-Detail, Supporting Pane) adapted for diverse screen sizes and foldable form factors.
* **Coil:** Asynchronous, Kotlin-first image loading library optimized for Jetpack Compose.

---

## Networking

* **Retrofit:** Type-safe REST client converting HTTP APIs into annotated Kotlin interfaces. Integrated with `kotlinx.serialization` or `Moshi` for JSON parsing and Kotlin Coroutines (`suspend` functions) for non-blocking execution.
* **OkHttp3:** Transport layer beneath Retrofit providing HTTP/2 and HTTP/3 support, connection pooling, GZIP compression, and response caching. Configured with custom `Interceptor` chains for automated OAuth token refreshes, dynamic header injection, request signing, and logging.
* **GraphQL + Apollo Kotlin + Hygraph:** Type-safe GraphQL integration leveraging Apollo Kotlin to generate immutable Kotlin models directly from `.graphql` schema files and queries. Paired with Hygraph as a headless CMS API to eliminate over-fetching/under-fetching, enforce strict response typing, and leverage normalized caching (`ApolloStore`).

---

## Local Data Storage & Persistence

* **Room:** Abstraction layer over SQLite providing compile-time verification of raw SQL queries. Supports reactive data observation through Kotlin `Flow`, automated schema migration handling, custom type converters, and multi-entity relationship mapping.
* **Proto DataStore:** Modern replacement for `SharedPreferences` that uses Protocol Buffers (Protobuf) for strongly typed, schema-backed key-value and object storage. Ensures type safety without runtime parsing overhead and executes all I/O asynchronously via Kotlin Coroutines and `Flow`.

---

## Code Maintenance & Safeguards

* **Lint:** Static analysis tool used to identify structural issues, unused XML namespaces, unsupported API calls across Android target versions, and deprecated elements.
* **Spotless:** Automated code formatting tool enforcing consistent code style (indentation, spacing, imports) with custom formatting rules applied during build and commit checks.
* **DependencyGuard:** Guardrail tool that monitors dependency tree changes to prevent unintended version updates or experimental library inflation during builds.
* **Badging:** Guards against unintended changes in Android manifest declarations, permissions, or exported components.

---

## Performance Engineering

* **Layout Inspector:** Real-time debugging tool that monitors UI composition, tracking recomposition counts and skip counts for Composables during active user interactions.
* **Compose Compiler Reports:** Generates stability reports for composable functions and data structures to identify unstable parameters that trigger unnecessary recomposition loops.
* **ReportFullyDrawn API:** Signals to the OS that the application is fully rendered and interactive, enabling Android to optimize cold startup times and pre-warm I/O.
* **Benchmark (Macrobenchmark & Microbenchmark):** Automated performance tests used to measure and prevent regressions during app startup, list scrolling, and screen transitions.
* **Baseline Profiles:** Pre-compiled rules shipped with the APK/AAB to bypass JIT compilation and interpretation on initial launch, reducing cold startup time by ~30%.
* **Profiling (Systrace / Perfetto):** CPU trace tools to measure main thread load, rendering bottlenecks, and system calls. The Memory Profiler offers live views of memory allocation and garbage collection events.
* **JankStats:** Captures frame drops alongside real-time application state metadata to pinpoint exact UI workflows causing frame stutters.
* **Tracing API:** Manual code instrumentation using `trace("label")` blocks to observe execution time in Systrace and Perfetto.

---

## CI/CD & Automation

* **GitHub Actions:** Automated continuous integration and delivery pipeline consisting of two workflows:
1. **Validation Workflow:** Runs linting, formatting verification, static checks, and unit tests on commits/PRs.
2. **Release Workflow:** Generates signed production artifacts (AAB/APK) and handles automated deployment.


* **Renovate / Dependabot:** Automated dependency scanning tools. Renovate is used to manage dependency version catalogs, create PRs, and maintain shareable version configurations across repositories.

---

## Test-Driven Development (TDD) & Quality Assurance

* **Frameworks:**
* **JUnit 4/5:** Core framework for running fast unit tests on the JVM.
* **Robolectric:** JVM-based simulation of the Android framework for running unit and UI tests without physical hardware.
* **ComposeTestRule & Espresso:** UI testing frameworks for verifying user interactions, layout trees, and accessibility across Compose and View hierarchies.


* **Network & Stream Testing:**
* **MockWebServer:** Scriptable local HTTP server used to intercept network requests and simulate API responses, HTTP errors, timeouts, and edge cases.
* **Turbine:** Declarative assertion library for testing Kotlin `Flow` reactive streams sequentially (`awaitItem()`, `awaitComplete()`, `awaitError()`).


* **Dependency Injection in Tests:**
* **Hilt Testing (`HiltAndroidTest` / `HiltAndroidRule`):** Enables dependency injection in JVM and instrumentation tests. Supports replacing production modules with test fakes using `@UninstallModules` and `@TestInstallIn`.


* **Test Types Practiced:**
* Unit Tests (Domain UseCases, Repositories, ViewModels)
* Integration & Network Interceptor Tests
* UI & Screenshot Tests
* Instrumentation & End-to-End (E2E) Tests
