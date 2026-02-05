# Three.js Volumetric Light Beam (Godot Port)

A high-performance WebGL port of the "Fake Volumetric Light" shader, originally created for Godot and inspired by the lighting techniques used in **Half-Life 2**.

This effect uses **Cylindrical Billboarding** in the vertex shader to force a flat quad to always face the camera while rotating exclusively around the light beam's axis. This creates a convincing 3D volumetric cone effect without the heavy performance cost of raymarching.

[**Live Demo**](https://tront.xyz/threejs-volumetric-beam/)

## The Backstory

This shader technique was popularized by **Passivestar**, who analyzed the specific Z-billboard lighting tricks used in *Half-Life 2* (specifically map `d1_canals_08`) to achieve atmospheric volume cheaply in 2004.

I (Tront) ported this logic from Godot's shading language to raw GLSL for Three.js, adding procedural noise generation to simulate dust motes and smoke without needing external texture assets.

**Original Sources:**
- [Light beam shader in Godot](https://passivestar.xyz/posts/light-beam-shader-in-godot/) - Passivestar's blog post
- [Bluesky Discussion](https://bsky.app/profile/passivestar.bsky.social/post/3lyge6cxs5k24) - Original social thread

## Features

- **HL2-Style Z-Billboarding** - Locks rotation to the beam axis for a 3D illusion on a 2D plane
- **Procedural Noise** - Generates "dust" and "smoke" textures on the fly via HTML5 Canvas (no external assets)
- **Camera Fading** - Automatically fades out when looking directly down the beam (the "paper-thin" angle)
- **Instanced Config** - Global atmosphere settings with local variations for color, size, and orientation
- **GUI Controls** - Tweak beam shape, noise, and atmosphere in real-time
- **Spawn System** - Add randomized lights on the fly with optimized `.clone()` materials

## How It Works

### The Trick

It's a flat quad. That's it. Valve did this in Half-Life 2 (2004, map `d1_canals_08`) — not real volumetric fog, just a billboard that rotates to face the camera. Your brain does the rest. Passivestar reverse-engineered the technique and rebuilt it in Godot; this port brings it to raw GLSL on Three.js.

### Vertex Shader: Cylindrical Billboarding

A `cross` product between the camera-to-beam direction and the beam's local Y-axis gives us a new "right" vector. We rebuild the quad's vertex positions using this right vector (horizontal) and the beam axis (vertical). The result: the quad always faces you, but it's locked to its axis — it won't flip or tumble. The shader also computes a `dot` product between the view direction and beam axis to produce a fade value. When you look straight down the beam and would see it edge-on (paper-thin), it fades out gracefully instead of breaking the illusion.

### Fragment Shader: Shape + Atmosphere

The cone shape comes from `pow(uv.y, 1.0 - curve)` — this interpolates the width from a narrow tip to a wide base along the beam's length. A horizontal `smoothstep` mask gives the edges a soft falloff instead of a hard cutout. A vertical power-fade dims the beam toward the end.

For atmosphere, three noise texture samples scroll in different directions: two control alpha (dust/smoke density), one drives UV distortion so the noise isn't static. World-position offsets (`vWorldPos.xz`, `.y`) ensure each beam samples a different region of the noise texture, so beams next to each other don't look identical.

### Performance

One shared `ShaderMaterial` is created, then `.clone()`'d for each beam — this shares the compiled shader program on the GPU while giving each beam its own uniform values (color, length, time offset). Additive blending with no depth writes means no sorting overhead. The noise texture is generated once on a `<canvas>` at startup — zero external assets, zero network requests. Compare this to raymarched volumetrics (dozens of texture samples per pixel per frame) and the cost difference is night and day.

## Usage

Drop `index.html` into any web server or run locally:

```bash
# Python
python -m http.server

# Node
npx serve

# Or just open index.html directly in a browser
```

## Controls

- **Orbit** - Left click + drag
- **Zoom** - Scroll wheel
- **Pan** - Right click + drag
- **GUI** - Top-right panel for beam shape, atmosphere, and spawning

## Credits

- **Original Shader Concept:** [Passivestar](https://passivestar.xyz/)
- **Three.js Port:** [Trent Sterling (Tront)](https://tront.xyz) & Gemini

## About the Author

**Trent Sterling (Tront)** - Game developer with over 10 years of experience specializing in Unity, C#, VR, and multiplayer networking.

- [tront.xyz](https://tront.xyz)
- [Twitter (@Trent_Sterling)](https://twitter.com/Trent_Sterling)
- [GitHub](https://github.com/TrentSterling)

## License

[MIT](LICENSE)
