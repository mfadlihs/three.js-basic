# Three.js Basic 

Sharing Knowledge SuitConnect Suitmedia

## Node Version

This knowledge sharing based on Node v18.17.1. Make sure to use this version

## Instalation

### Install Vite

Open a command prompt on your system somewhere.

```bash
npm create vite@latest
```

Don't forget to use Vanilla and TypeScript

```
√ Select a framework: » Vanilla
√ Select a variant: » TypeScript
```

run these commands bellow to install required packages

```bash
npm i
```
```bash
npm run dev
```

### Install three

Open the terminal in projects directory and execute these commands below.

```bash
npm install -D three @types/three
```

### Replace Contents of Remaining Files

Replace the contents of `index.html`, `main.ts` and `style.css` with the code below.

./index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js TypeScript</title>
  </head>

  <body>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

./src/style.css

```css
body {
  overflow: hidden;
  margin: 0px;
}
```

./src/main.ts

```ts
import './style.css'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'

const scene = new THREE.Scene()

const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
camera.position.z = 1.5

const renderer = new THREE.WebGLRenderer()
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
})

new OrbitControls(camera, renderer.domElement)

const geometry = new THREE.BoxGeometry()
const material = new THREE.MeshNormalMaterial({ wireframe: true })

const cube = new THREE.Mesh(geometry, material)
scene.add(cube)

function animate() {
  requestAnimationFrame(animate)

  cube.rotation.x += 0.005
  cube.rotation.y += 0.005

  renderer.render(scene, camera)
}

animate()
```
