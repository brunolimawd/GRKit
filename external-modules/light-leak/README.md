# Light Leak

An optional GRKit module that simulates light entering the edge of a film camera.

Current beta: **`0.1.0-beta.1`** (GRM revision `16`). See [`VERSION`](VERSION).
The public SemVer is independent of the numeric revision required by GRKit.

In **ADJ → GRK → Light Leak**, choose a style:

| Reference style | Original entry | 1 · Original | 2 | 3 |
| --- | --- | --- | --- | --- |
| Slit Flare | Right | White beam, red curtain and cyan reflection | Shorter cut with a shallow gold edge | Wide diagonal beam with a detached blue trail |
| Burnt Edge | Right | Irregular bright seam and red-orange spill | Localized pale corner burn | Deep copper burn with a plum halo |
| Film Band | Bottom | Raised gold band with a red lip and cool fringe | Two red flashes close to the edge | Deeper, slanted gold and cyan band |
| Amber Veil | Top | Amber pool with a diagonal red tail | Compact, honey-colored pool | Long, rust-colored sweep in the opposite direction |
| Prismatic Arc | Right | Red, gold, teal and blue arc | Tight blue-green crescent close to the edge | Wide gold arc with a separate blue-violet echo |
| Color Fog | Top | Diagonal gold, teal, blue and rose wash | Cool sea-green and violet | Warm rose and gold on the opposite slant |

Use **Fn → Strength** for Weak, Medium, or Strong. **Fn → Leak Position**
offers Right, Left, Top, Bottom, Random, and Original for all six reference
styles. Original uses the entry in the table. **Fn → Variation** offers
**1** (the original look), **2** and **3** (different light shapes and
palettes within the same style), or **Auto** (the default). Every style
has all three variations on every side. Auto chooses one per shot
without changing the style, strength or side.

The **Random** style independently draws a reference style, strength and
side, without changing the saved Strength or Leak Position. It also
draws a variation if Variation is set to Auto, or uses the fixed
number otherwise. All draws are shared by the JPEG and its previews, and
consecutive shots can repeat. Each draw uses
the capture metadata and light samples from the main JPEG plane, not a
firmware clock call or mutable state carried between encodes.

Version 11 removed the original six prototypes. Only when upgrading
from before v11, the style starts at **Off**; choose it again in ADJ.
On upgrade from v11 or later, all existing settings are kept. Select
Variation **1** for the exact look from v12.

The six reference styles calculate the light at the native sample centers
of each JPEG plane, without enlarging a raster mask. The source photos
served only as visual references; no pixels, scratches, or watermarks
from them are included. Clear areas do not darken the image, and clipped
highlights retain their original values. The directions are tied to the
sensor and rotate with portrait shots. Monochrome receives the same
brightness lift without tint. The defaults are Off, Medium, Original
position and Auto variation.

The effect is written into the JPEG and its previews. It does not modify
the raw sensor data, has no live-view preview, and is not stored in Recipes.
It runs on the visible image after the color look and Glow, before Film-like
Grain and Date Imprint. It does not color the added border.

## Viewfinder icon

With **Show Icon** on, the left of the still viewfinder shows the active
style as a white symbol when DISP shows full information. One, two, or
three white segments show Weak, Medium, or Strong; the remaining
segments are gray. Random shows a die without segments because its
strength is drawn at capture. Off shows nothing.

**Show Icon** defaults to **On** and is saved. If an earlier installation
saved Off, use **Fn → Show Icon → On** to enable it. The ADJ icons are
unchanged; the viewfinder symbol is never part of the saved image.

The badge uses a live-view layer so it can coexist with all six registered
GRKit status icons. While the badge is visible, GRKit hides the camera's
native grid, as it does for other live-view layers. Turn Show Icon off to
restore the previous grid behavior, or use the GRKit Guides module.

## Build and install

With the [module toolchain](../../docs/modules.md#prerequisites) and a GRKit
package that supports module ABI 2.7, run these commands from the repository root:

```sh
mkdir -p build/light-leak
grkit module test --dir external-modules/light-leak
grkit module build --dir external-modules/light-leak --out build/light-leak/LITLEAK.GRM
```

If `ld.lld` is not on your `PATH`, add `--lld <path>` as described in the
guide. The build needs no firmware file and refuses to overwrite a module
or its objects folder, so use a fresh output directory for a rebuild.

1. Copy `LITLEAK.GRM` to `GRKIT/MODULES` on the SD card with GRKit already installed.
2. Power on the camera. Once GRK appears in ADJ, check that Light Leak is listed.
3. If it is missing, look for `LITLEAK.GRM` in `GRKIT/REPORT.TXT`. The
   external module needs a free slot among the ten module files the core can list.

To update a previous installation, replace `GRKIT/MODULES/LITLEAK.GRM` with
the new build. Do not copy it to `SCRIPT/GRKIT/MODULES`.

Version 15 uses its own **OFF** tile for the disabled row. It keeps the
v14 looks and Random choices unchanged. The renderer only evaluates the
support of each light source and reuses the exposure curve for the whole
JPEG and its previews. It still evaluates at native sample centers.

For a first camera test, compare Off and Medium in JPEG+RAW, try a portrait
shot and a cropped shot, then check single shots and bursts with
[`GRKIT/PROFILE`](../../docs/modules.md#measure-the-cpu-use-on-the-camera).
To remove the module, delete `LITLEAK.GRM`. If the camera stops before
the menu opens, use [`GRKIT/SAFE`](../../docs/modules.md#if-the-camera-stops-at-power-on).
