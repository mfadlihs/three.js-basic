## Geometry

Three.js has many classes for creating common geometries.

- [BoxGeometry](https://threejs.org/docs/#api/en/geometries/BoxGeometry)
- [CircleGeometry](https://threejs.org/docs/#api/en/geometries/CircleGeometry)
- [CylinderGeometry](https://threejs.org/docs/#api/en/geometries/CylinderGeometry)
- [ConeGeometry](https://threejs.org/docs/#api/en/geometries/ConeGeometry)
- [EdgesGeometry](https://threejs.org/docs/#api/en/geometries/EdgesGeometry)
- [ExtrudeGeometry](https://threejs.org/docs/#api/en/geometries/ExtrudeGeometry)
- [ShapeGeometry](https://threejs.org/docs/#api/en/geometries/ShapeGeometry)
- [LatheGeometry](https://threejs.org/docs/#api/en/geometries/LatheGeometry)
- [PlaneGeometry](https://threejs.org/docs/#api/en/geometries/PlaneGeometry)
- [RingGeometry](https://threejs.org/docs/#api/en/geometries/RingGeometry)
- [SphereGeometry](https://threejs.org/docs/#api/en/geometries/SphereGeometry)
- [TextGeometry](https://threejs.org/docs/?q=textge#examples/en/geometries/TextGeometry)
- [TorusGeometry](https://threejs.org/docs/#api/en/geometries/TorusGeometry)
- [TorusKnotGeometry](https://threejs.org/docs/#api/en/geometries/TorusKnotGeometry)
- [TubeGeometry](https://threejs.org/docs/#api/en/geometries/TubeGeometry)
- [PolyhedronGeometry](https://threejs.org/docs/#api/en/geometries/PolyhedronGeometry)
- [DodecahedronGeometry](https://threejs.org/docs/#api/en/geometries/DodecahedronGeometry)
- [IcosahedronGeometry](https://threejs.org/docs/#api/en/geometries/IcosahedronGeometry)
- [OctahedronGeometry](https://threejs.org/docs/#api/en/geometries/OctahedronGeometry)
- [TetrahedronGeometry](https://threejs.org/docs/#api/en/geometries/TetrahedronGeometry)
- [ParametricGeometry](https://threejs.org/docs/?q=param#examples/en/geometries/ParametricGeometry)
- [WireframeGeometry](https://threejs.org/docs/#api/en/geometries/WireframeGeometry)

## Code

`index.html`

```diff
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Three.js TypeScript Tutorials by Sean Bradley : https://sbcode.net/threejs</title>
  </head>

  <body>
-   <div id="debug"></div>
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
```

`./src/main.ts`

```ts
import './style.css'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import Stats from 'three/addons/libs/stats.module.js'
import { GUI } from 'lil-gui'

const scene = new THREE.Scene()
scene.add(new THREE.AxesHelper(5))

const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
)
camera.position.set(-2, 4, 5)

const renderer = new THREE.WebGLRenderer()
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

new OrbitControls(camera, renderer.domElement)

const boxGeometry = new THREE.BoxGeometry()
//const sphereGeometry = new THREE.SphereGeometry()
//const icosahedronGeometry = new THREE.IcosahedronGeometry()

console.log(boxGeometry)

const material = new THREE.MeshNormalMaterial({
  wireframe: true,
})

const cube = new THREE.Mesh(boxGeometry, material)
//cube.position.x = -4
scene.add(cube)

// const sphere = new THREE.Mesh(sphereGeometry, material)
// sphere.position.x = 0
// scene.add(sphere)

// const icosahedron = new THREE.Mesh(icosahedronGeometry, material)
// scene.add(icosahedron)
// sphere.position.x = 4

window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
})

const stats = new Stats()
document.body.appendChild(stats.dom)

const gui = new GUI()

// const cubeData = {
//   width: 1,
//   height: 1,
//   depth: 1,
//   widthSegments: 1,
//   heightSegments: 1,
//   depthSegments: 1
// }

const cubeFolder = gui.addFolder('Cube')
const cubeRotationFolder = cubeFolder.addFolder('Rotation')
cubeRotationFolder.add(cube.rotation, 'x', 0, Math.PI * 2, 0.01)
cubeRotationFolder.add(cube.rotation, 'y', 0, Math.PI * 2, 0.01)
cubeRotationFolder.add(cube.rotation, 'z', 0, Math.PI * 2, 0.01)
const cubePositionFolder = cubeFolder.addFolder('Position')
cubePositionFolder.add(cube.position, 'x', -10, 10)
cubePositionFolder.add(cube.position, 'y', -10, 10)
cubePositionFolder.add(cube.position, 'z', -10, 10)
const cubeScaleFolder = cubeFolder.addFolder('Scale')
cubeScaleFolder.add(cube.scale, 'x', -5, 5, 0.1) // .onFinishChange(function() { console.log(cube.geometry) })
cubeScaleFolder.add(cube.scale, 'y', -5, 5, 0.1)
cubeScaleFolder.add(cube.scale, 'z', -5, 5, 0.1)
// cubeFolder
//   .add(cubeData, 'width', 1, 30)
//   .onChange(regenerateBoxGeometry)
//   .onFinishChange(function() { console.log(cube.geometry) })
// cubeFolder.add(cubeData, 'height', 1, 30).onChange(regenerateBoxGeometry)
// cubeFolder.add(cubeData, 'depth', 1, 30).onChange(regenerateBoxGeometry)
// cubeFolder.add(cubeData, 'widthSegments', 1, 30).onChange(regenerateBoxGeometry)
// cubeFolder.add(cubeData, 'heightSegments', 1, 30).onChange(regenerateBoxGeometry)
// cubeFolder.add(cubeData, 'depthSegments', 1, 30).onChange(regenerateBoxGeometry)
cubeFolder.open()

// function regenerateBoxGeometry() {
//   const newGeometry = new THREE.BoxGeometry(
//     cubeData.width,
//     cubeData.height,
//     cubeData.depth,
//     cubeData.widthSegments,
//     cubeData.heightSegments,
//     cubeData.depthSegments
//   )
//   cube.geometry.dispose()
//   cube.geometry = newGeometry
// }

// const sphereData = {
//   radius: 1,
//   widthSegments: 8,
//   heightSegments: 6,
//   phiStart: 0,
//   phiLength: Math.PI * 2,
//   thetaStart: 0,
//   thetaLength: Math.PI
// }
// const sphereFolder = gui.addFolder('Sphere')
// sphereFolder.add(sphereData, 'radius', 0.1, 30).onChange(regenerateSphereGeometry)
// sphereFolder.add(sphereData, 'widthSegments', 1, 32).onChange(regenerateSphereGeometry)
// sphereFolder.add(sphereData, 'heightSegments', 1, 16).onChange(regenerateSphereGeometry)
// sphereFolder.add(sphereData, 'phiStart', 0, Math.PI * 2).onChange(regenerateSphereGeometry)
// sphereFolder.add(sphereData, 'phiLength', 0, Math.PI * 2).onChange(regenerateSphereGeometry)
// sphereFolder.add(sphereData, 'thetaStart', 0, Math.PI).onChange(regenerateSphereGeometry)
// sphereFolder.add(sphereData, 'thetaLength', 0, Math.PI).onChange(regenerateSphereGeometry)
// sphereFolder.open()

// function regenerateSphereGeometry() {
//   const newGeometry = new THREE.SphereGeometry(
//     sphereData.radius,
//     sphereData.widthSegments,
//     sphereData.heightSegments,
//     sphereData.phiStart,
//     sphereData.phiLength,
//     sphereData.thetaStart,
//     sphereData.thetaLength
//   )
//   sphere.geometry.dispose()
//   sphere.geometry = newGeometry
// }

// const icosahedronData = {
//   radius: 1,
//   detail: 0
// }
// const icosahedronFolder = gui.addFolder('Icosahedron')
// icosahedronFolder.add(icosahedronData, 'radius', 0.1, 10).onChange(regenerateIcosahedronGeometry)
// icosahedronFolder.add(icosahedronData, 'detail', 0, 5).step(1).onChange(regenerateIcosahedronGeometry)
// icosahedronFolder.open()

// function regenerateIcosahedronGeometry() {
//   const newGeometry = new THREE.IcosahedronGeometry(icosahedronData.radius, icosahedronData.detail)
//   icosahedron.geometry.dispose()
//   icosahedron.geometry = newGeometry
// }

function animate() {
  requestAnimationFrame(animate)

  renderer.render(scene, camera)

  stats.update()
}

animate()
```
