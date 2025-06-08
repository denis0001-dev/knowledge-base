#code #kotlin #fix 

Add the following to root of the file where the `internal` API is used:
```kotlin
@file:Suppress(
	"CANNOT_OVERRIDE_INVISIBLE_MEMBER",
	"INVISIBLE_MEMBER",
	"INVISIBLE_REFERENCE"
)
```
