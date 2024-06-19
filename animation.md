## Animation Looping

To start, replace remove canvas in html and replace `./src/main.ts` with this script below.

`index.html`

```diff
- <canvas id="canvas"></canvas>
```

`./src/main.ts`

```ts
import './style.css'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import Stats from 'three/addons/libs/stats.module.js'

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

const stats = new Stats()
document.body.appendChild(stats.dom)

function animate() {
  requestAnimationFrame(animate)

  cube.rotation.x += 0.01
  cube.rotation.y += 0.01

  renderer.render(scene, camera)

  stats.update()
}

animate()
```

The `window.requestAnimationFrame()` method tells the browser you wish to perform an animation. It requests the browser to call a user-supplied callback function before the next repaint.

The frequency of calls to the callback function will generally match the display refresh rate. The most common refresh rate is 60hz, (60 cycles/frames per second), though 75hz, 120hz, and 144hz are also widely used. `requestAnimationFrame()` calls are paused in most browsers when running in background tabs or hidden <iframe>s, in order to improve performance and battery life.

```diff
...
const stats = new Stats()
document.body.appendChild(stats.dom)

+ const clock = new THREE.Clock()
+ let delta

function animate() {
  requestAnimationFrame(animate)

+ delta = clock.getDelta()

+ cube.rotation.x += delta
+ cube.rotation.y += delta

  renderer.render(scene, camera)

  stats.update()
}

animate()
```

## On Demand Rendering

Making the code render only when the OrbitControls properties change or the screen is resized.

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
  renderer.render(scene, camera) // render whenever the screen size changes
})

const controls = new OrbitControls(camera, renderer.domElement)
controls.addEventListener('change', () => {
  renderer.render(scene, camera) // render whenever the OrbitControls changes
})

const geometry = new THREE.BoxGeometry()
const material = new THREE.MeshNormalMaterial({ wireframe: true })

const cube = new THREE.Mesh(geometry, material)
scene.add(cube)

renderer.render(scene, camera) // render once when the scene has been setup
```

## References

<a href="https://developer.mozilla.org/en-US/docs/Web/API/window/requestAnimationFrame" alt="requestAnimationFrame" target="_blank">window.requestAnimationFrame (MDN)</a>
