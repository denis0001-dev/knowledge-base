#kotlin #fix #gradle #code 

The following error appears when the following code is added to `build.gradle.kts` > `kotlin` > `sourceSets`:
```kotlin
val iosMain by creating {  
    dependsOn(commonMain.get())  
    iosX64Main.get().dependsOn(this)  
    iosArm64Main.get().dependsOn(this)  
    iosSimulatorArm64Main.get().dependsOn(this)  
}
```

## Fix
1. Add the following line to `build.gradle.kts` > `kotlin`:
	```kotlin
	applyDefaultHierarchyTemplate()
	```
2. Change `creating {...}` to `getting`:
	```kotlin
	val iosMain by getting
	```