# Showtime — Cross-Platform Movie App

![Kotlin](https://img.shields.io/badge/Kotlin-2.3-7F52FF)
![Kotlin Multiplatform](https://img.shields.io/badge/Kotlin%20Multiplatform-Android%20%7C%20iOS%20%7C%20Desktop-7F52FF)
![Compose Multiplatform](https://img.shields.io/badge/Compose%20Multiplatform-1.10-4285F4)

A movie discovery app built with **Kotlin Multiplatform** and **Compose Multiplatform**. One shared codebase (UI included) runs on **Android, iOS and desktop (JVM)**. Users can browse and filter a movie catalog, keep favorites and a watchlist, watch trailers and test their movie knowledge in a timed quiz with a global leaderboard.

Built for the *Mobile App Development* course at the School of Computing (RAF), Belgrade.

<!-- Screenshots: add 3–4 images here, e.g.
<p align="center">
  <img src="docs/movies.png" width="200"/> <img src="docs/details.png" width="200"/> <img src="docs/quiz.png" width="200"/>
</p>
-->

---

## Features

- **Authentication.** Sign up and log in with a bearer token. The session is persisted with DataStore, and any `401` response logs the user out automatically.
- **Movie catalog.** Server-side paging, sorting and filters by genre, year range, minimum rating and title search.
- **Offline support.** The current catalog page is cached in a Room database and shown with an offline banner when the network is unavailable.
- **Movie details.** Overview, cast, images, and trailers that open in YouTube through platform-specific code.
- **Favorites and watchlist.** Synced with the backend.
- **Quiz.**
  - 10 questions in 60 seconds, generated from the cached catalog.
  - Three question types: *guess the movie* from a poster or backdrop, *guess the release year*, and *guess the lead actor*.
  - The score combines correct answers and remaining time. Results are saved locally and submitted to a global leaderboard.
- **Profile.** Best score, number of plays and quiz history.

---

## Architecture

The app follows a layered *data → domain → UI* structure, and every screen uses an **MVI** pattern:

- `XContract`: an immutable `UiState` plus the `UiEvent`s a screen can send.
- `XViewModel`: consumes events from a `SharedFlow`, reduces state into a `StateFlow`, and calls repositories.
- `XScreen`: stateless Compose UI that renders `UiState` and emits events.

```
composeApp/src/
├── commonMain/          # shared code: ~all of the app
│   ├── data/
│   │   ├── remote/      # Ktorfit APIs, DTOs, error mapping
│   │   ├── local/       # Room (entities, DAOs, database), DataStore
│   │   ├── mapper/      # DTO ↔ entity ↔ domain mappers
│   │   └── repository/  # Auth, Movies, Favorites, Watchlist, Quiz
│   ├── domain/model/    # domain models
│   ├── di/              # Koin module
│   └── ui/              # screens (Contract/ViewModel/Screen), navigation, components
├── androidMain/         # actual: DataStore & Room factories, YouTube opener
├── iosMain/             # actual: DataStore & Room factories, YouTube opener
└── jvmMain/             # actual: DataStore & Room factories, YouTube opener, desktop entry point
```

Platform-specific pieces (database and DataStore file locations, opening YouTube) are implemented with Kotlin `expect`/`actual`, so everything else is written once.

---

## Tech stack

| Area | Libraries |
|---|---|
| Language & UI | Kotlin 2.3, Compose Multiplatform, Material 3 |
| Navigation | Navigation Compose (type-safe routes) |
| Networking | Ktor client, Ktorfit, kotlinx.serialization |
| Persistence | Room (KMP) with bundled SQLite, DataStore Preferences |
| Dependency injection | Koin |
| Images | Coil 3 |
| Concurrency | Kotlin Coroutines & Flow |

The backend is the course REST API (`rma.finlab.rs`). Movie images are served from TMDB.

---

## Running the app

**Requirements:** JDK 17+, Android Studio (or IntelliJ IDEA) with the Kotlin Multiplatform plugin. For iOS you also need macOS with Xcode.

```bash
git clone https://github.com/mldndjsc/showtime-project.git
cd showtime-project
```

| Platform | Command |
|---|---|
| Android | `./gradlew :composeApp:assembleDebug`, or run the `composeApp` configuration from Android Studio |
| Desktop | `./gradlew :composeApp:run` |
| iOS | open `iosApp/` in Xcode and run |

On Windows use `.\gradlew.bat` instead of `./gradlew`.
