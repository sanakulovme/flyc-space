# Black Hole

Powered by [@abbosby](https://x.com/abbosby)

A real-time, physically-based Schwarzschild black hole in a single HTML file.
Open `index.html` in any modern browser — no build, no server, no dependencies.

Nothing here is a texture or a fake. Every pixel is a light ray traced
**backwards through curved spacetime** until it either falls past the event
horizon, hits the accretion disk, or escapes to the stars.

## Controls (Google Maps style)

| Gesture | Action |
| --- | --- |
| Drag | Orbit (with inertia) |
| Right-drag / Shift-drag | Pan |
| Scroll / Pinch | Zoom |
| Double-click | Zoom in (Shift = out) |
| Two-finger drag | Pan (touch) |
| Arrow keys | Orbit |
| `+` / `-` | Zoom |
| `R` | Reset bearing and tilt |
| `O` | Auto-orbit |
| `M` | Ambient sound on/off |
| `P` | Save a PNG (stamped `x.com/abbosby`) |
| `F` | Fullscreen |
| `S` | Settings |
| `H` | Hide the interface |

The compass at the bottom right spins with your bearing — click it to snap
back. The slider beside the zoom buttons is the tilt, from edge-on to
top-down. Settings are remembered between visits.

## Sound

The speaker button plays `sound.mp3` from this folder on a loop, fading in
and out. It is **off by default** and never starts on its own — browsers
block audio until you interact with the page anyway, so if the preference is
restored as "on" it waits for your first click. Volume lives in Settings. If
the file is missing or cannot be decoded, the button turns itself back off
and says so rather than failing silently.

Despite the name, `sound.mp3` is AAC audio in an MP4 container. Browsers
decode it by content, not by file extension, so it plays fine.

## What you are looking at

- **The shadow** — the black disc is about 2.6 r<sub>s</sub> across, larger
  than the r<sub>s</sub> event horizon itself, because light passing near the
  hole is bent into it.
- **The ring over the top** — that is the *far* side of the disk. Light from
  behind the hole is bent up and over toward you, so a flat disk looks folded.
  The arc underneath is the same trick from below.
- **The thin bright circle** — the photon ring, light that orbited the hole
  one or more times before escaping.
- **The bright side** — gas on the approaching side is Doppler-boosted and
  blue-shifted; the receding side dims and reddens. Dial it with the
  *Doppler beaming* slider (the famous Interstellar frame used almost none,
  which is why that image is symmetric).
- **Time dilation** in the readout is the rate of your clock compared to one
  infinitely far away, at your current distance.

## The physics

Units are G = c = M = 1, so the Schwarzschild radius r<sub>s</sub> = 2 and the
UI quotes distances in r<sub>s</sub>.

Null geodesics are integrated in Cartesian coordinates using the conserved
angular momentum h = |x × v| of each ray:

```
d²x/dλ² = -1.5 · r_s · h² · x / r⁵
```

with an adaptive step — short where spacetime is curved and near the disk,
long once the ray is far away. The disk is a plane at y = 0; every crossing is
solved for exactly by interpolation, which is what makes the multiple lensed
images appear for free. Each crossing is shaded as an emitting and absorbing
slab, so a grazing ray saturates toward the surface brightness instead of
stacking emission, and the gas is given a Keplerian shear (ω ∝ r^-3/2) that
winds the noise into trailing filaments.

Then: relativistic Doppler and gravitational redshift per crossing, a
blackbody-ish colour ramp, HDR bloom, ACES tonemapping.

## Performance

It is a ray marcher, so cost scales with pixels × steps. The quality presets
trade step count against resolution, and *Adaptive resolution* (on by default)
keeps the frame rate near 60 by scaling the render buffer between 42% and
100% based on measured frame time.

Resolution is the only thing that flexes. Step count never drops while you
drag, because the step budget *is* the geometry: starve it and rays near the
hole stop mid-flight, which punches a star-filled black blob through the
shadow and eats the lensed far side of the disk. If it feels heavy, drop to
**Medium** or **Low** in Settings, or shrink the window. The live readout in
the top left shows fps and the actual render resolution.

Requires WebGL2.

## SEO and metadata

The page is published at <https://flyc.space/>. Search and social metadata
lives in the `<head>` of `index.html`:

- Title (56 chars) and description (145 chars), both inside what Google
  actually shows in results.
- `canonical`, `robots` (`max-image-preview:large`), `theme-color` and an
  inline SVG favicon — no extra file requests.
- Open Graph and Twitter/X cards pointing at `og-image.jpg`
  (1200&times;630, a real frame from the renderer).
- JSON-LD `WebApplication` structured data carrying the author, the
  copyright holder, the copyright notice and the licence URL.
- `robots.txt` and `sitemap.xml`, including an image entry for the preview.

A canvas is opaque to crawlers, so the **About** dialog (the `i` button, or
press `I`) carries ~380 words of real prose about the physics. It is ordinary
visible content — no hidden text.

If the domain ever changes, update `https://flyc.space/` in `index.html`
(canonical, `og:url`, `og:image`, `twitter:image`, JSON-LD), in `robots.txt`
and in `sitemap.xml`.

To regenerate the preview image: open the site, press `H` to hide the
interface, frame the shot, then `P` to save a PNG — or re-run the capture at
exactly 1200&times;630.

## Copyright

&copy; 2026 [@abbosby](https://x.com/abbosby). All rights reserved. See
[LICENSE](LICENSE) — no permission is granted to copy, modify, host or
redistribute this work without written permission.

## Running it

Double-click `index.html`. If your browser ever refuses to load `sound.mp3`
from a `file://` page, serve the folder instead:

```bash
python -m http.server 8777
```

then open <http://localhost:8777>. (`.claude/launch.json` wires this up for
the Claude Code preview pane.)
