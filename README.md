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

The "magic" is **Cylindrical/Axial Billboarding**:

1. The vertex shader computes a `cross` product between the camera direction and the beam's local Y-axis
2. This forces the quad to always face the camera, but only rotates around the beam axis
3. The fragment shader procedurally draws the cone shape, soft edges, vertical fade, and scrolling noise layers

No raymarching. No volume textures. Just a flat quad that looks 3D.

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
