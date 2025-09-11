#code #kotlin #fix  #gradle 

```kotlin
kotlin {
    compilerOptions {
        freeCompilerArgs.add("-Xsuppress-warning=<WARNING_ID>")
    }
}
```

`<WARNING_ID>` - the identifier of the compiler warning.