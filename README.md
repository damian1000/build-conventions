# build-conventions

The shared Gradle build platform for the `damian1000` repositories, published via JitPack:
convention plugins plus a shared version catalog (`:catalog`). A repository applies one
convention plugin and then declares only what is specific to it — its dependencies and, for
an application, its main class.

## Plugins

| Plugin                                    | For               | Provides                                                                                                    |
| ----------------------------------------- | ----------------- | ----------------------------------------------------------------------------------------------------------- |
| `io.github.damian1000.kotlin-conventions` | Kotlin JVM        | JDK 25 toolchain, pinned Kotlin, 90% JaCoCo instruction gate, Spotless (ktlint + Prettier), OWASP, JUnit 6. |
| `io.github.damian1000.java-conventions`   | plain Java        | The same, with Spotless JDK-agnostic Java hygiene in place of ktlint.                                       |
| `io.github.damian1000.root-conventions`   | multi-module root | Repo-wide Spotless (Gradle scripts, CI config, docs) and the OWASP aggregate; modules apply a plugin above. |

Front-end tooling in `kotlin-conventions` is content-driven: web assets under
`src/main/resources/web` are Prettier-formatted under `spotlessCheck`, and a `package.json`
wires `npm run lint` (ESLint) into `check`. A repository is different only because of what it
holds — the CI pipeline stays identical.

## Consuming

`settings.gradle` — add JitPack and map the plugin id to this module:

```groovy
pluginManagement {
    repositories {
        gradlePluginPortal()
        mavenCentral()
        maven { url 'https://jitpack.io' }
    }
    resolutionStrategy {
        eachPlugin {
            if (requested.id.namespace == 'io.github.damian1000') {
                useModule("com.github.damian1000.build-conventions:plugins:${requested.version}")
            }
        }
    }
}
```

`build.gradle`:

```groovy
plugins {
    id 'io.github.damian1000.kotlin-conventions' version '0.4.10'
    id 'application' // if the repository is an application
}

dependencies {
    // only what this repository actually needs
}
```

## Version catalog

`gradle/libs.versions.toml` holds only versions two or more repos genuinely share — hamcrest,
slf4j, the Oracle driver pair, testcontainers, flyway, kafka-clients, commons-lang3, h2, gson.
Application-specific dependencies stay in each repo's build file. Import it in
`settings.gradle`:

```groovy
dependencyResolutionManagement {
    repositories {
        maven { url 'https://jitpack.io' }
    }
    versionCatalogs {
        create('deps') {
            from 'com.github.damian1000.build-conventions:catalog:0.4.10'
        }
    }
}
```

Then in `build.gradle`: `testImplementation deps.hamcrest`, `runtimeOnly deps.slf4j.simple`.
