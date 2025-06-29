#code #android #compose #kotlin

Use the following code to create the view:

```kotlin
val view = ComposeView(this).apply {
    setContent {
        // content
    }

    // Trick The ComposeView into thinking we are tracking lifecycle
    val lifecycleOwner = ComposeLifecycleOwner()
    lifecycleOwner.performRestore(null)
    lifecycleOwner.handleLifecycleEvent(Lifecycle.Event.ON_CREATE)
    setViewTreeLifecycleOwner(lifecycleOwner)
    setViewTreeSavedStateRegistryOwner(lifecycleOwner)
}
```

Create the overlay like this:

```kotlin
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
    // set the layout parameters of the window
    val params = WindowManager.LayoutParams( 
	    // Shrink the window to wrap the content rather
        // than filling the screen
        WindowManager.LayoutParams.WRAP_CONTENT,
        WindowManager.LayoutParams.WRAP_CONTENT,
        // Display it on top of other application windows
        WindowManager.LayoutParams.TYPE_APPLICATION_OVERLAY,
        // Don't let it grab the input focus
        WindowManager.LayoutParams.FLAG_NOT_FOCUSABLE,
        // Make the underlying application window visible
        // through any transparent parts
        PixelFormat.TRANSLUCENT
    )
  
    val view = ComposeView {
        // Compose content
    }.apply {
        // Use the LifecycleOwner to fix the ComposeView
        val lifecycleOwner = ComposeLifecycleOwner()
        lifecycleOwner.performRestore(null)
        lifecycleOwner.handleLifecycleEvent(Lifecycle.Event.ON_CREATE)
        setViewTreeLifecycleOwner(lifecycleOwner)
        setViewTreeSavedStateRegistryOwner(lifecycleOwner)
    }

    // Define the position of the window within the screen
    params.gravity = Gravity.CENTER
    val windowManager = context.getSystemService(WINDOW_SERVICE) as WindowManager
    try {
        // check if the view is already inflated or present in the window
        if (view.windowToken == null && view.parent == null) {
            windowManager.addView(view, params);
        }
    } catch (e: Exception) {
        // handle the exception
    }
}
```

__ComposeLifecycleOwner__:

```kotlin
import android.os.Bundle
import androidx.lifecycle.Lifecycle
import androidx.lifecycle.LifecycleOwner
import androidx.lifecycle.LifecycleRegistry
import androidx.savedstate.SavedStateRegistry
import androidx.savedstate.SavedStateRegistryController
import androidx.savedstate.SavedStateRegistryOwner

class ComposeLifecycleOwner : SavedStateRegistryOwner, LifecycleOwner {
    private var mLifecycleRegistry: LifecycleRegistry = LifecycleRegistry(this)
    private var mSavedStateRegistryController: SavedStateRegistryController = SavedStateRegistryController.create(this)

    fun onCreate() {
        performRestore(null)
        handleLifecycleEvent(Lifecycle.Event.*ON_CREATE*)
        lifecycle.currentState = Lifecycle.State.*CREATED*
    }

    fun onStart() {
        handleLifecycleEvent(Lifecycle.Event.*ON_START*)
        lifecycle.currentState = Lifecycle.State.*STARTED*
    }

    fun onDestroy() {
        handleLifecycleEvent(Lifecycle.Event.*ON_DESTROY*)
        lifecycle.currentState = Lifecycle.State.*DESTROYED*
    }

    /**
     * @return True if the Lifecycle has been initialized.
     */
    val isInitialized get() = true

    override val lifecycle = mLifecycleRegistry

    fun setCurrentState(state: Lifecycle.State) {
        mLifecycleRegistry.currentState = state
    }

    fun handleLifecycleEvent(event: Lifecycle.Event) {
        mLifecycleRegistry.handleLifecycleEvent(event)
    }

    override val savedStateRegistry: SavedStateRegistry
        get() = mSavedStateRegistryController.savedStateRegistry

    fun performRestore(savedState: Bundle?) {
        mSavedStateRegistryController.performRestore(savedState)
    }
  
    fun performSave(outBundle: Bundle) {
        mSavedStateRegistryController.performSave(outBundle)
    }
}
```

Source: https://gist.github.com/handstandsam/6ecff2f39da72c0b38c07aa80bbb5a2f

Thanks to @CommonsWare for giving me the idea!