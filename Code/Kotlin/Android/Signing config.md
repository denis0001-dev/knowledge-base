#code #android #kotlin #gradle #security 

## Setup
First, create a keystore file in `keys/keystore.jks`. Then, create `keys/keystore.properties`:

```toml
keyPassword=<password>
storePassword=<password>
```

Replace `<password>` with the actual password for both the key password and the keystore password.
## Gradle config
Add the following block in `build.gradle.kts` > `android`:

```kotlin
signingConfigs {  
    create("release") {  
        val keystoreProperties = Properties().apply {  
            load(FileInputStream(rootProject.file("keys/keystore.properties")))  
        }  
        storeFile = rootProject.file("keys/release.jks")  
        keyAlias = "release"  
        storePassword = keystoreProperties["storePassword"].toString()  
        keyPassword = keystoreProperties["keyPassword"].toString()  
        enableV3Signing = true  
    }  
}
```

Add the following line to the `release` build type config (`build.gradle.kts` > `android` > `buildTypes` > `release`):

```kotlin
signingConfig = signingConfigs.getByName("release")
```