# Materials

## Description

An introduction to the Three.js Materials.

- [THREE.Material](https://threejs.org/docs/#api/en/materials/Material)

All these classes below inherit methods and properties from the Material base class.

- [LineBasicMaterial](https://threejs.org/docs/#api/en/materials/LineBasicMaterial)
  - [LineDashedMaterial](https://threejs.org/docs/#api/en/materials/LineDashedMaterial)
- [MeshBasicMaterial](https://threejs.org/docs/#api/en/materials/MeshBasicMaterial)
- [MeshDepthMaterial](https://threejs.org/docs/#api/en/materials/MeshDepthMaterial)
- [MeshDistanceMaterial](https://threejs.org/docs/#api/en/materials/MeshDistanceMaterial)
- [MeshLambertMaterial](https://threejs.org/docs/#api/en/materials/MeshLambertMaterial)
- [MeshMatcapMaterial](https://threejs.org/docs/#api/en/materials/MeshMatcapMaterial)
- [MeshNormalMaterial](https://threejs.org/docs/#api/en/materials/MeshNormalMaterial)
- [MeshPhongMaterial](https://threejs.org/docs/#api/en/materials/MeshPhongMaterial)
- [MeshStandardMaterial](https://threejs.org/docs/#api/en/materials/MeshStandardMaterial)
  - [MeshPhysicalMaterial](https://threejs.org/docs/#api/en/materials/MeshPhysicalMaterial)
- [MeshToonMaterial](https://threejs.org/docs/#api/en/materials/MeshToonMaterial)
- [PointsMaterial](https://threejs.org/docs/#api/en/materials/PointsMaterial)
- [ShaderMaterial](https://threejs.org/docs/#api/en/materials/ShaderMaterial)
  - [RawShaderMaterial](https://threejs.org/docs/#api/en/materials/RawShaderMaterial)
- [ShadowMaterial](https://threejs.org/docs/#api/en/materials/ShadowMaterial)
- [SpriteMaterial](https://threejs.org/docs/#api/en/materials/SpriteMaterial)

## Code

`./src/main.ts`

```ts
import './style.css'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import Stats from 'three/addons/libs/stats.module.js'
import { GUI } from 'lil-gui'

const scene = new THREE.Scene()
scene.environment = new THREE.CubeTextureLoader()
  .setPath('https://cdn.jsdelivr.net/gh/mfadlihs/three.js-basic@main/assets/images/')
  .load(['px.png', 'nx.png', 'py.png', 'ny.png', 'pz.png', 'nz.png'])

const axesHelper = new THREE.AxesHelper(5)
scene.add(axesHelper)

const gridHelper = new THREE.GridHelper()
gridHelper.position.y = -1
scene.add(gridHelper)

const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
)
camera.position.set(0, 2, 7)

const renderer = new THREE.WebGLRenderer({ antialias: true })
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

const controls = new OrbitControls(camera, renderer.domElement)
controls.enableDamping = true

const boxGeometry = new THREE.BoxGeometry()

const sphereGeometry = new THREE.SphereGeometry()

const icosahedronGeometry = new THREE.IcosahedronGeometry()

const planeGeometry = new THREE.PlaneGeometry()

const torusKnotGeometry = new THREE.TorusKnotGeometry()

const material = new THREE.MeshStandardMaterial()

const cube = new THREE.Mesh(boxGeometry, material)
cube.position.set(5, 0, 0)
scene.add(cube)

const sphere = new THREE.Mesh(sphereGeometry, material)
sphere.position.set(3, 0, 0)
scene.add(sphere)

const icosahedron = new THREE.Mesh(icosahedronGeometry, material)
icosahedron.position.set(0, 0, 0)
scene.add(icosahedron)

const plane = new THREE.Mesh(planeGeometry, material)
plane.position.set(-2, 0, 0)
scene.add(plane)

const torusKnot = new THREE.Mesh(torusKnotGeometry, material)
torusKnot.position.set(-5, 0, 0)
scene.add(torusKnot)

window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
})

const stats = new Stats()
document.body.appendChild(stats.dom)

const options = {
  side: {
    FrontSide: THREE.FrontSide,
    BackSide: THREE.BackSide,
    DoubleSide: THREE.DoubleSide,
  },
}

const gui = new GUI()

const materialFolder = gui.addFolder('THREE.Material')
materialFolder
  .add(material, 'transparent')
  .onChange(() => (material.needsUpdate = true))
materialFolder.add(material, 'opacity', 0, 1, 0.01)
materialFolder
  .add(material, 'alphaTest', 0, 1, 0.01)
  .onChange(() => updateMaterial())
materialFolder.add(material, 'visible')
materialFolder
  .add(material, 'side', options.side)
  .onChange(() => updateMaterial())
materialFolder.open()

function updateMaterial() {
  material.side = Number(material.side) as THREE.Side
  material.needsUpdate = true
}

function animate() {
  requestAnimationFrame(animate)

  controls.update()

  renderer.render(scene, camera)

  stats.update()
}

animate()
```

[12. Common Materials](common-materials.md)
