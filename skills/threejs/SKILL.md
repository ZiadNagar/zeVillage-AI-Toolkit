---
name: threejs
description: "Use this skill when building 3D web experiences with Three.js — scene setup, cameras, lights, materials, animation loops, model loading, post-processing, shaders, particle systems, and React Three Fiber integration."
license: Complete terms in LICENSE.txt
---

# Three.js 3D Web Development

## When to Use

- Building interactive 3D scenes, product viewers, or data visualizations in the browser.
- Loading and displaying GLTF/GLB 3D models on a webpage.
- Creating animated backgrounds, particle effects, or shader-driven visuals.
- Integrating 3D content into a React application via React Three Fiber.
- Adding post-processing effects (bloom, SSAO, depth of field) to a 3D scene.

## Core Concepts

### Scene Setup

Every Three.js application needs three things: a scene, a camera, and a renderer.

```js
import * as THREE from "three";

const scene = new THREE.Scene();
scene.background = new THREE.Color(0x111111);

const camera = new THREE.PerspectiveCamera(
  75, // field of view (degrees)
  window.innerWidth / window.innerHeight, // aspect ratio
  0.1, // near clipping plane
  1000, // far clipping plane
);
camera.position.set(0, 2, 5);

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
document.body.appendChild(renderer.domElement);
```

### Cameras

| Camera                                                    | Use Case                                                               |
| --------------------------------------------------------- | ---------------------------------------------------------------------- |
| `PerspectiveCamera(fov, aspect, near, far)`               | Mimics human eye — most 3D scenes.                                     |
| `OrthographicCamera(left, right, top, bottom, near, far)` | No perspective distortion — isometric views, 2D overlays, UI elements. |

```js
// Orthographic camera sized to viewport
const aspect = window.innerWidth / window.innerHeight;
const frustum = 5;
const orthoCamera = new THREE.OrthographicCamera(
  -frustum * aspect,
  frustum * aspect,
  frustum,
  -frustum,
  0.1,
  100,
);
```

### Lights

| Light                                               | Description                                  |
| --------------------------------------------------- | -------------------------------------------- |
| `AmbientLight(color, intensity)`                    | Uniform fill — no shadows.                   |
| `DirectionalLight(color, intensity)`                | Parallel rays like sunlight — casts shadows. |
| `PointLight(color, intensity, distance)`            | Emits in all directions from a point.        |
| `SpotLight(color, intensity, distance, angle)`      | Cone-shaped — flashlights, stage lights.     |
| `HemisphereLight(skyColor, groundColor, intensity)` | Sky/ground gradient — natural outdoor fill.  |

```js
const ambientLight = new THREE.AmbientLight(0xffffff, 0.4);
scene.add(ambientLight);

const dirLight = new THREE.DirectionalLight(0xffffff, 1.0);
dirLight.position.set(5, 10, 7);
dirLight.castShadow = true;
dirLight.shadow.mapSize.set(2048, 2048);
scene.add(dirLight);
```

### Geometries & Meshes

A mesh combines a geometry (shape) with a material (appearance).

```js
const geometry = new THREE.BoxGeometry(1, 1, 1);
const material = new THREE.MeshStandardMaterial({
  color: 0x4488ff,
  roughness: 0.4,
  metalness: 0.6,
});
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);
```

Common geometries: `BoxGeometry`, `SphereGeometry`, `PlaneGeometry`, `CylinderGeometry`, `TorusGeometry`, `TorusKnotGeometry`, `IcosahedronGeometry`, `BufferGeometry` (custom).

### Materials

| Material               | Characteristics                                             |
| ---------------------- | ----------------------------------------------------------- |
| `MeshBasicMaterial`    | Unlit, no shadows — debug, overlays.                        |
| `MeshStandardMaterial` | PBR with roughness/metalness — general use.                 |
| `MeshPhysicalMaterial` | Extended PBR — clearcoat, transmission, sheen, iridescence. |
| `MeshPhongMaterial`    | Legacy Blinn-Phong shading — cheaper, less realistic.       |
| `ShaderMaterial`       | Custom vertex/fragment shaders — full control.              |
| `RawShaderMaterial`    | Like ShaderMaterial but no built-in uniforms/attributes.    |

```js
// Glass-like physical material
const glassMaterial = new THREE.MeshPhysicalMaterial({
  color: 0xffffff,
  transmission: 0.95,
  roughness: 0.05,
  thickness: 0.5,
  ior: 1.5,
  envMapIntensity: 1.0,
});
```

## Animation Loop

Use `requestAnimationFrame` for the render loop.

```js
const clock = new THREE.Clock();

function animate() {
  requestAnimationFrame(animate);

  const delta = clock.getDelta(); // seconds since last frame
  const elapsed = clock.getElapsedTime();

  // Rotate the cube
  cube.rotation.x += 0.5 * delta;
  cube.rotation.y += 0.8 * delta;

  renderer.render(scene, camera);
}

animate();
```

## Controls

### OrbitControls

```js
import { OrbitControls } from "three/addons/controls/OrbitControls.js";

const controls = new OrbitControls(camera, renderer.domElement);
controls.enableDamping = true;
controls.dampingFactor = 0.05;
controls.minDistance = 2;
controls.maxDistance = 20;
controls.maxPolarAngle = Math.PI / 2; // prevent going below ground

// Must call update() in the animation loop when damping is enabled
function animate() {
  requestAnimationFrame(animate);
  controls.update();
  renderer.render(scene, camera);
}
```

## Loading 3D Models

### GLTF / GLB Loader

```js
import { GLTFLoader } from "three/addons/loaders/GLTFLoader.js";
import { DRACOLoader } from "three/addons/loaders/DRACOLoader.js";

const dracoLoader = new DRACOLoader();
dracoLoader.setDecoderPath("/draco/");

const gltfLoader = new GLTFLoader();
gltfLoader.setDRACOLoader(dracoLoader);

gltfLoader.load(
  "/models/robot.glb",
  (gltf) => {
    const model = gltf.scene;
    model.traverse((child) => {
      if (child.isMesh) {
        child.castShadow = true;
        child.receiveShadow = true;
      }
    });
    scene.add(model);

    // Play animations if present
    if (gltf.animations.length) {
      const mixer = new THREE.AnimationMixer(model);
      const action = mixer.clipAction(gltf.animations[0]);
      action.play();
      // Update mixer in the animation loop: mixer.update(delta);
    }
  },
  (progress) =>
    console.log(
      `Loading: ${((progress.loaded / progress.total) * 100).toFixed(0)}%`,
    ),
  (error) => console.error("Model load error:", error),
);
```

## Shadows

```js
// 1. Enable shadows on the renderer
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;

// 2. Light must cast shadows
dirLight.castShadow = true;
dirLight.shadow.camera.near = 0.5;
dirLight.shadow.camera.far = 50;
dirLight.shadow.camera.left = -10;
dirLight.shadow.camera.right = 10;
dirLight.shadow.camera.top = 10;
dirLight.shadow.camera.bottom = -10;

// 3. Meshes must cast and/or receive shadows
cube.castShadow = true;

const floor = new THREE.Mesh(
  new THREE.PlaneGeometry(20, 20),
  new THREE.MeshStandardMaterial({ color: 0x222222 }),
);
floor.rotation.x = -Math.PI / 2;
floor.receiveShadow = true;
scene.add(floor);
```

## Particle Systems

Use `Points` with `BufferGeometry` for performant particle effects.

```js
const particleCount = 5000;
const positions = new Float32Array(particleCount * 3);

for (let i = 0; i < particleCount * 3; i++) {
  positions[i] = (Math.random() - 0.5) * 20;
}

const particleGeometry = new THREE.BufferGeometry();
particleGeometry.setAttribute(
  "position",
  new THREE.BufferAttribute(positions, 3),
);

const particleMaterial = new THREE.PointsMaterial({
  size: 0.05,
  color: 0x88ccff,
  transparent: true,
  opacity: 0.8,
  sizeAttenuation: true, // smaller when further away
  depthWrite: false, // prevent z-fighting
  blending: THREE.AdditiveBlending,
});

const particles = new THREE.Points(particleGeometry, particleMaterial);
scene.add(particles);

// Animate particles in the render loop
function animate() {
  requestAnimationFrame(animate);
  particles.rotation.y += 0.001;
  renderer.render(scene, camera);
}
```

## Custom Shaders

### ShaderMaterial Example

```js
const shaderMaterial = new THREE.ShaderMaterial({
  uniforms: {
    uTime: { value: 0 },
    uColor: { value: new THREE.Color(0x4488ff) },
  },
  vertexShader: `
    uniform float uTime;
    varying vec2 vUv;
    varying float vElevation;

    void main() {
      vUv = uv;
      vec3 pos = position;
      float elevation = sin(pos.x * 3.0 + uTime) * 0.2
                      + sin(pos.y * 2.0 + uTime * 0.5) * 0.15;
      pos.z += elevation;
      vElevation = elevation;
      gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
    }
  `,
  fragmentShader: `
    uniform vec3 uColor;
    varying vec2 vUv;
    varying float vElevation;

    void main() {
      float brightness = vElevation * 2.0 + 0.6;
      gl_FragColor = vec4(uColor * brightness, 1.0);
    }
  `,
  side: THREE.DoubleSide,
});

const plane = new THREE.Mesh(
  new THREE.PlaneGeometry(4, 4, 64, 64),
  shaderMaterial,
);
scene.add(plane);

// Update time uniform in the animation loop
function animate() {
  requestAnimationFrame(animate);
  shaderMaterial.uniforms.uTime.value = clock.getElapsedTime();
  renderer.render(scene, camera);
}
```

## Post-Processing

```js
import { EffectComposer } from "three/addons/postprocessing/EffectComposer.js";
import { RenderPass } from "three/addons/postprocessing/RenderPass.js";
import { UnrealBloomPass } from "three/addons/postprocessing/UnrealBloomPass.js";
import { SMAAPass } from "three/addons/postprocessing/SMAAPass.js";

const composer = new EffectComposer(renderer);
composer.addPass(new RenderPass(scene, camera));

const bloom = new UnrealBloomPass(
  new THREE.Vector2(window.innerWidth, window.innerHeight),
  0.5, // strength
  0.4, // radius
  0.85, // threshold
);
composer.addPass(bloom);

const smaa = new SMAAPass(window.innerWidth, window.innerHeight);
composer.addPass(smaa);

// Replace renderer.render() with composer.render() in the animation loop
function animate() {
  requestAnimationFrame(animate);
  composer.render();
}
```

## Responsive Resizing

```js
window.addEventListener("resize", () => {
  const width = window.innerWidth;
  const height = window.innerHeight;

  camera.aspect = width / height;
  camera.updateProjectionMatrix();

  renderer.setSize(width, height);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));

  // Update composer size if using post-processing
  composer.setSize(width, height);
});
```

## React Three Fiber Integration

React Three Fiber (R3F) is a React renderer for Three.js.

```jsx
import { Canvas, useFrame } from "@react-three/fiber";
import { OrbitControls, Environment, useGLTF } from "@react-three/drei";
import { useRef } from "react";

function SpinningBox() {
  const meshRef = useRef();
  useFrame((state, delta) => {
    meshRef.current.rotation.y += delta * 0.5;
  });

  return (
    <mesh ref={meshRef} castShadow>
      <boxGeometry args={[1, 1, 1]} />
      <meshStandardMaterial color="#4488ff" roughness={0.4} metalness={0.6} />
    </mesh>
  );
}

function Model({ url }) {
  const { scene } = useGLTF(url);
  return <primitive object={scene} />;
}

export default function Scene() {
  return (
    <Canvas shadows camera={{ position: [0, 2, 5], fov: 75 }}>
      <ambientLight intensity={0.4} />
      <directionalLight position={[5, 10, 7]} intensity={1} castShadow />
      <SpinningBox />
      <Model url="/models/robot.glb" />
      <OrbitControls enableDamping />
      <Environment preset="city" />
    </Canvas>
  );
}
```

### Drei Helpers

`@react-three/drei` provides pre-built abstractions:

| Helper             | Purpose                                  |
| ------------------ | ---------------------------------------- |
| `<OrbitControls>`  | Camera orbit, pan, zoom.                 |
| `<Environment>`    | HDR environment maps (preset or custom). |
| `<ContactShadows>` | Soft floor shadows without shadow maps.  |
| `<Float>`          | Gentle floating animation on children.   |
| `<Text3D>`         | Extruded 3D text from font JSON.         |
| `<Html>`           | Embed HTML inside the 3D scene.          |
| `useGLTF`          | Hook to load GLTF/GLB models.            |
| `useTexture`       | Hook to load textures.                   |

## Performance Best Practices

### Instancing

Render thousands of identical meshes with a single draw call.

```js
const count = 10000;
const dummy = new THREE.Object3D();
const instancedMesh = new THREE.InstancedMesh(
  new THREE.SphereGeometry(0.1, 8, 8),
  new THREE.MeshStandardMaterial({ color: 0x44aaff }),
  count,
);

for (let i = 0; i < count; i++) {
  dummy.position.set(
    (Math.random() - 0.5) * 50,
    (Math.random() - 0.5) * 50,
    (Math.random() - 0.5) * 50,
  );
  dummy.updateMatrix();
  instancedMesh.setMatrixAt(i, dummy.matrix);
}

scene.add(instancedMesh);
```

### Level of Detail (LOD)

```js
const lod = new THREE.LOD();

const highDetail = new THREE.Mesh(
  new THREE.SphereGeometry(1, 64, 64),
  material,
);
const medDetail = new THREE.Mesh(new THREE.SphereGeometry(1, 16, 16), material);
const lowDetail = new THREE.Mesh(new THREE.SphereGeometry(1, 6, 6), material);

lod.addLevel(highDetail, 0); // visible when distance < 10
lod.addLevel(medDetail, 10); // visible when distance 10–50
lod.addLevel(lowDetail, 50); // visible when distance > 50

scene.add(lod);
```

### General Tips

- **Limit draw calls**: merge geometries, use instancing, use texture atlases.
- **Frustum culling**: enabled by default (`mesh.frustumCulled = true`). Ensure bounding spheres are correct.
- **Dispose resources**: call `geometry.dispose()`, `material.dispose()`, `texture.dispose()` when removing objects.
- **Pixel ratio cap**: `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))` — avoids expensive rendering on high-DPI screens.
- **Shadow map size**: use the minimum resolution that looks acceptable (512–2048).
- **Texture sizes**: keep power-of-two dimensions, compress with KTX2/Basis.
- **Use `BufferGeometry`**: never use the legacy `Geometry` class.
- **Monitor performance**: use `stats.js` or the browser performance panel.

## Anti-Patterns

- Forgetting to call `controls.update()` in the loop when damping is enabled.
- Creating new materials or geometries inside the render loop — allocate outside and reuse.
- Not disposing textures/geometries when removing objects — causes memory leaks.
- Setting shadow map size too high on mobile — degrades performance significantly.
- Using `MeshPhysicalMaterial` everywhere — reserve for surfaces that need clearcoat/transmission.
- Loading uncompressed GLTF instead of GLB with Draco compression.
