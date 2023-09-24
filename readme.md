# Stylus
## _Stylus Pressure Input for Java, via JNI_

This is a **_mod_** of the original **Stylus JNI** from [lectureStudio](https://github.com/lectureStudio/stylus).

The original is a
> Java native interface (JNI)  implementation for pressure-sensitive input devices. 

With sample app. However, it's over restrictive and only registers (stylus listener) correctly for JFrame specifically. Descendant classes or other Window classes will throw exception.

**My mod fixes this flaw and enhances the ✨Magic ✨ by making it check for and hence allow _any_ `java.awt.Window`**, instead of JFrame only. Tested OK for Windows 7, 10, and JDK 19, 20, when built with [Visual Studio](https://visualstudio.microsoft.com/) (VS) 2019, 2022.

## To Build

- Use [CMake](https://cmake.org/) to import Stylus' `src/main/cpp/CMakeLists.txt.
- Generate VS projects and solution.
- Build.


## Use With

Stylus JNI needs [org.lecturestudio.stylus ](https://github.com/lectureStudio/stylus/tree/main/stylus/src/main/java/org/lecturestudio/stylus) Java classes for the stylus listener interface and events.
Or [my mod of above](https://github.com/sdneon/stylus-lib).

It's okay to enhance those classes, so long as the _base_ interface used by JNI to transfer stylus info through, remains intact.

For sample app, refer to  either:
* [lectureStudio](https://github.com/lectureStudio/stylus)'s sample app if needed. Although that uses Google Guice which obfuscate's things somewhat, making it harder to understand the app's codes.
* my [stylus-sample](https://github.com/sdneon/stylus-sample).

### Deficiency

A major deficiency is that unlike MouseEvent's, the Stylus events do NOT have keyboard modifiers!

To remedy that, you could listen globally and track modifier key (ALT, SHIFT, CTRL) presses and releases. Then mesh it into StylusEvent, whenever one is received. E.g.:

```java
//put these in some singleton manager, like AwtStylusManager in lectureStudio demo app
protected boolean alt = false, shift = false, ctrl = false;

KeyboardFocusManager mgr = KeyboardFocusManager.getCurrentKeyboardFocusManager();
mgr.addKeyEventDispatcher(new KeyEventDispatcher() {
    @Override
    public boolean dispatchKeyEvent(KeyEvent e) {
        alt = e.isAltDown();
        shift = e.isShiftDown();
        ctrl = e.isControlDown();
        return false; //do NOT consume event
    }
});

```

## Thanks

Thanks to the many developers who tried providing pen pressure inputs for Java over the years. However, many has fallen into disrepair, say owing to obsolete DLL depenencies that can no longer by found.
* Micheal Mcguffin for his informative article and research on pen inputs for Java, [here (external link to article)](https://www.michaelmcguffin.com/code/JWinPointer/).
  * He recommends JWinPointer, but alas, I can't get it to work correctly.
* [JPen](https://jpen.sourceforge.net/html-home/index.html) [(git)](https://github.com/nicarran/jpen).
  * Unfortunately, this Wacom's obsolete (and lost) WinTab SDK, so can't get this running either, without old WinTab DLL. The latter are impossible to find.