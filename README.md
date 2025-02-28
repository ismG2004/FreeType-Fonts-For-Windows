# FreeType Fonts For Windows

A number of fonts that are primarily available on macOS and Linux distributions, 
such as DejaVu Sans, Bitstream Vera, Menlo and etc., 
do not contain TrueType hinting instructions as these fonts were designed for 
systems utilizing FreeType (or FreeType-like engine in case of macOS) rather than TrueType.

On Apple devices, displays typically have very high pixel densities, 
making font hinting less noticeable. 
Since this technology was intended for low PPI monitors, **macOS does not use it**. 
This makes those fonts look good only if you are using Mac or Linux. 

These links here explain a bit more: 
* [Typotheque](https://www.typotheque.com/articles/hinting)
* [lucia scarlet on X.com](https://x.com/luciascarlet/status/1857909771757867082)

<p align="center">
  <img src="https://i.imgur.com/aCDJQrk.png" alt="HintedVsUnhintedComparison">
</p>

* (Windows) While not immediately noticable at larger sizes, an unhinted font rendered at 
  smaller sizes (16px and below) can appear jiggly, aliased and broken.

### How to obtain the data about my font?

[FontDrop](https://fontdrop.info/?darkmode=true) is a useful site 
for visualizing and identifying the features available in a specific font. 
You'll need it to determine which features to keep or omit  
when using `pyftsubset`.

## Tool Installation

**Please note that gftools requires [Python 3.7](http://www.python.org/download/) or later.**

Please install these tools using pip:

    pip install gftools

## Requirements

* [Python 3.7 or later](http://www.python.org/download/)

## How to use

As many of Linux distros ship with unhinted fonts, we need to implement 
hinting for better representation on Windows. 
This can be done by autohinting the fonts using `gftools` 
(which includes a `ttfautohint` wrapper):

    gftools autohint input.ttf --output output.ttf

## Removing Ligatures

Some monospace typefaces, such as **Cascadia Code** and **JetBrains Mono**, 
include well-designed OpenType Features that 
enhance their overall look and feel. However, in less maintained typefaces, 
like DejaVu Sans Mono or Menlo, these features can sometimes appear inconsistent and out of place.

This section will focus on removing ligatures to generate a plain monospaced font file.

### Method 1. Using `pyftsubset`

This method involves the usage of the `pyftsubset` which is bundled with `gftools`.

* It's specifically designed to remove unnecessary glyphs and features from the font, 
  optimizing it and making it smaller.
* Might strip additional metadata.

Issue the following command to delete ligatures from `INPUT.TTF`:

    pyftsubset.exe INPUT.TTF '*' --layout-features-='liga' --output-file=OUTPUT.TTF

**_NOTE:_**  `pyftsubset` might return the following warning which is totally fine:

    "WARNING: FFTM NOT subset; don't know how to subset; dropped" 

### Method 2. Using Kotlin Script

This method involves using a Kotlin script that requires JDK 8 or higher 
(minimum tested version: JDK 8) to run.

* Allows changing the font name if it's needed for some reason.
* On the other hand, font file size is generally larger compared to Method 1.

Open `GenerateNLVersion.kt`, uncomment the following code snippet 
and change `replace()` arguments accordingly if a name change is needed:

```kotlin
val xPath = XPathFactory.newInstance().newXPath()
val nameRecords = (xPath.evaluate("/ttFont/name/namerecord", doc, XPathConstants.NODESET) as NodeList).asList()
nameRecords.forEach {
    if (!it.textContent.contains("trademark")) {
        it.textContent = it.textContent
            .replace("Menlo", "Menlo NL")
    }
}
```

## Credits

* Google Fonts team
    * <https://github.com/googlefonts/gftools>

* [Konstantin Bulenkov](https://github.com/bulenkov) for his 
[Kotlin script](https://github.com/JetBrains/JetBrainsMono/blob/master/scripts/GenerateNLVersion.kt) 
for generating No Ligature versions of fonts