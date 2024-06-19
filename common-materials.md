# Common Materials

## Description

In this lesson, we will introduce four of the most commonly used materials that you will see used in Three.js.

- [MeshBasicMaterial](https://threejs.org/docs/#api/en/materials/MeshBasicMaterial)
- [MeshNormalMaterial](https://threejs.org/docs/#api/en/materials/MeshNormalMaterial)
- [MeshPhongMaterial](https://threejs.org/docs/#api/en/materials/MeshPhongMaterial)
- [MeshStandardMaterial](https://threejs.org/docs/#api/en/materials/MeshStandardMaterial)

There are other materials, but we will discuss those, plus more details, about each material as we progress.

In this lesson, we will start by focusing on several important factors about materials and why you might choose one over the other.

## Code

`index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js TypeScript</title>
  </head>

  <body>
    <div class="label">MeshBasicMaterial</div>
    <div class="label">MeshNormalMaterial</div>
    <div class="label">MeshPhongMaterial</div>
    <div class="label">MeshStandardMaterial</div>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

`./src/style.css`

```css
body {
  overflow: hidden;
  margin: 0px;
}

.label {
  position: absolute;
  color: #ffffff;
  font-family: monospace;
  pointer-events: none;
  text-align: center;
  width: 75px;
  height: 25px;
  font-size: 1vw;
  filter: drop-shadow(2px 2px 1px #000000);
  display: none;
}
```

`./src/main.ts`

```ts
import './style.css'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import Stats from 'three/addons/libs/stats.module.js'
import { GUI } from 'lil-gui'

const scene = new THREE.Scene()
// scene.environment = new THREE.CubeTextureLoader().setPath('https://cdn.jsdelivr.net/gh/mfadlihs/three.js-basic@main/assets/images/').load(['px.png', 'nx.png', 'py.png', 'ny.png', 'pz.png', 'nz.png'])

const gridHelper = new THREE.GridHelper()
scene.add(gridHelper)

const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  100
)
camera.position.set(-1, 4, 2.5)

const renderer = new THREE.WebGLRenderer({ antialias: true })
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
})

const controls = new OrbitControls(camera, renderer.domElement)
controls.enableDamping = true

const light = new THREE.DirectionalLight(undefined, Math.PI)
light.position.set(1, 1, 1)
scene.add(light)

const data = { color: 0x00ff00, labelsVisible: true }

const plane = new THREE.Mesh(
  new THREE.PlaneGeometry(10, 10),
  new THREE.MeshBasicMaterial({ color: 0x00ff00 })
)
plane.rotation.x = -Math.PI / 2
plane.visible = false
scene.add(plane)

const geometry = new THREE.IcosahedronGeometry(1, 1)

const meshes = [
  new THREE.Mesh(geometry, new THREE.MeshBasicMaterial({ color: data.color })),
  new THREE.Mesh(geometry, new THREE.MeshNormalMaterial({ flatShading: true })),
  new THREE.Mesh(
    geometry,
    new THREE.MeshPhongMaterial({ color: data.color, flatShading: true })
  ),
  new THREE.Mesh(
    geometry,
    new THREE.MeshStandardMaterial({ color: data.color, flatShading: true })
  ),
]

meshes[0].position.set(-3, 1, 0)
meshes[1].position.set(-1, 1, 0)
meshes[2].position.set(1, 1, 0)
meshes[3].position.set(3, 1, 0)

scene.add(...meshes)

const stats = new Stats()
document.body.appendChild(stats.dom)

const gui = new GUI()

const meshBasicMaterialFolder = gui.addFolder('MeshBasicMaterial')
meshBasicMaterialFolder.addColor(data, 'color').onChange(() => {
  ;(meshes[0].material as THREE.MeshBasicMaterial).color.set(data.color)
})
meshBasicMaterialFolder.add(meshes[0].material, 'wireframe')
meshBasicMaterialFolder.open()

const meshNormalMaterialFolder = gui.addFolder('MeshNormalMaterial')
meshNormalMaterialFolder
  .add(meshes[1].material as THREE.MeshNormalMaterial, 'flatShading')
  .onChange(() => {
    meshes[1].material.needsUpdate = true
  })
meshNormalMaterialFolder.add(meshes[1].material, 'wireframe')
meshNormalMaterialFolder.open()

const meshPhongMaterialFolder = gui.addFolder('MeshPhongMaterial')
meshPhongMaterialFolder.addColor(data, 'color').onChange(() => {
  ;(meshes[2].material as THREE.MeshPhongMaterial).color.set(data.color)
})
meshPhongMaterialFolder
  .add(meshes[2].material as THREE.MeshPhongMaterial, 'flatShading')
  .onChange(() => {
    meshes[2].material.needsUpdate = true
  })
meshPhongMaterialFolder.add(meshes[2].material, 'wireframe')
meshPhongMaterialFolder.open()

const meshStandardMaterialFolder = gui.addFolder('MeshStandardMaterial')
meshStandardMaterialFolder.addColor(data, 'color').onChange(() => {
  ;(meshes[3].material as THREE.MeshStandardMaterial).color.set(data.color)
})
meshStandardMaterialFolder
  .add(meshes[3].material as THREE.MeshStandardMaterial, 'flatShading')
  .onChange(() => {
    meshes[3].material.needsUpdate = true
  })
meshStandardMaterialFolder.add(meshes[3].material, 'wireframe')
meshStandardMaterialFolder.open()

const lightFolder = gui.addFolder('Light')
lightFolder.add(light, 'visible')
lightFolder.open()

const gridFolder = gui.addFolder('Grid')
gridFolder.add(gridHelper, 'visible')
gridFolder.open()

const labelsFolder = gui.addFolder('Labels')
labelsFolder.add(data, 'labelsVisible')
labelsFolder.open()

const labels = document.querySelectorAll<HTMLDivElement>('.label')

let x, y
const v = new THREE.Vector3()

function animate() {
  requestAnimationFrame(animate)

  controls.update()

  for (let i = 0; i < 4; i++) {
    v.copy(meshes[i].position)
    v.project(camera)

    x = ((1 + v.x) / 2) * innerWidth - 50
    y = ((1 - v.y) / 2) * innerHeight

    labels[i].style.left = x + 'px'
    labels[i].style.top = y + 'px'
    labels[i].style.display = data.labelsVisible ? 'block' : 'none'
  }

  renderer.render(scene, camera)

  stats.update()
}

animate()
```
