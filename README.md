# build-conventions

Shared Gradle build conventions for the `damian1000` repositories, published via JitPack. A
repository applies one convention plugin and then declares only what is specific to it — its
dependencies and, for an application, its main class.

## Plugins

| Plugin                                      | For          | Provides                                                                                                    |
| ------------------------------------------- | ------------ | ----------------------------------------------------------------------------------------------------------- |
| `io.github.damian1000.kotlin-conventions`   | Kotlin JVM   | JDK 25 toolchain, pinned Kotlin, 90% JaCoCo instruction gate, Spotless (ktlint + Prettier), OWASP, JUnit 6. |
| `io.github.damian1000.java-conventions`     | plain Java   | The same, with Spotless JDK-agnostic Java hygiene in place of ktlint.                                        |

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
                useModule("com.github.damian1000:build-conventions:${requested.version}")
            }
        }
    }
}
```

`build.gradle`:

```groovy
plugins {
    id 'io.github.damian1000.kotlin-conventions' version '0.1.0'
    id 'application' // if the repository is an application
}

dependencies {
    // only what this repository actually needs
}
```
