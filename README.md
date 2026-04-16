# Earth & Atmosphere Shading — Three.js Journey

Quick recap of the **Earth Shaders** lesson from [Three.js Journey](https://threejs-journey.com/) by Bruno Simon.

This repo implements a stylized **Earth rendering pipeline** using custom GLSL shaders: it blends **day and night textures** from sun direction, adds **cloud and specular details** from a packed texture, and renders a separate **transparent atmosphere shell** with color transitions between **daylight** and **twilight**.

## What this project covers

- **World-space varyings** from the vertex shader: **`vNormal`** and **`vPosition`** (via `modelMatrix`) so lighting-like computations remain stable in world space.
- **Sun-driven shading** with **`uSunDirection`** and `dot(normal, sunDirection)` to determine which parts of the globe are lit.
- **Day/night texture blending** in the Earth fragment shader using **`smoothstep`** for soft transitions at the terminator.
- **Packed utility texture usage** (`uSpecularCloudsTexture`): **`r`** for specular mask and **`g`** for cloud coverage, reducing texture fetch count.
- **Cloud pass + highlight pass**: cloud whitening applied only on lit regions, plus specular reflections shaped with `reflect`, view direction, and a shininess exponent.
- **Fresnel-based atmosphere tint** on Earth and a dedicated outer atmosphere mesh (`BackSide`, transparent) scaled slightly above the planet.
- **Interactive controls** in `main.ts`: atmosphere colors (`lil-gui`) and sun orientation (`THREE.Spherical` with `phi/theta`) propagated to both shaders.
- **Renderer consistency** with Three.js pipeline via **`tonemapping_fragment`** and **`colorspace_fragment`** after `gl_FragColor`.

## Tech stack

- **Three.js**, **Vite**, **`vite-plugin-glsl`**.
- **`ShaderMaterial`** with:
  - `src/shaders/earth/vertex.glsl`
  - `src/shaders/earth/fragment.glsl`
  - `src/shaders/atmosphere/vertex.glsl`
  - `src/shaders/atmosphere/fragment.glsl`
- **`OrbitControls`**, **`lil-gui`**, **`TextureLoader`**.

## What I learned

### 1) Lighting logic can be art-directed without full PBR

A simple **sun orientation** term (`dot(normal, sunDirection)`) is enough to drive convincing day/night blending and artistic transitions, especially when remapped with `smoothstep`.

### 2) One texture can carry multiple masks

Packing cloud and specular data in different channels of one texture is efficient and keeps shader logic tidy: one sample, two independent controls.

### 3) Fresnel is great for planetary edge readability

Using view-dependent Fresnel makes the limb of the planet glow naturally and helps the atmosphere read clearly from distance.

### 4) Separating atmosphere into its own mesh is powerful

Rendering a slightly larger sphere on `BackSide` with transparency gives clean control over atmospheric falloff and avoids overcomplicating the surface shader.

### 5) Shader uniforms become much clearer with interactive controls

Binding sun angles and atmosphere colors to `lil-gui` makes it easy to understand how each uniform affects final shading in real time.

## Run the project

```bash
npm install
npm run dev
```

## Credits

Part of the **Three.js Journey** course by Bruno Simon.
