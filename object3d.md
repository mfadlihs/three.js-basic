## Object 3D

**Object3D** is the base class for many objects in Three.js.

It provides all the common methods and properties for manipulating objects in 3D space.

The most common examples of Object3Ds that you will see are Meshes, Lights, Cameras and Groups of Object3Ds.

The full list of Object3D methods and properties can be found in the Three.js documentation at [THREE.Object3D](https://threejs.org/docs/?q=object#api/en/core/Object3D)

In this section I will introduce the most common things that you will do with an Object3D and that is to change,

- Rotation
- Position
- Scale
- Visibility

To begin, replace your `./src/main.ts` with the code below.

```ts
import './style.css'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import Stats from 'three/addons/libs/stats.module.js'
import { GUI } from 'lil-gui'

const scene = new THREE.Scene()
scene.add(new THREE.AxesHelper(5))

const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
camera.position.set(1, 2, 3)

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

const gui = new GUI()

const cubeFolder = gui.addFolder('Cube')
cubeFolder.add(cube, 'visible')
cubeFolder.open()

const positionFolder = cubeFolder.addFolder('Position')
positionFolder.add(cube.position, 'x', -5, 5)
positionFolder.add(cube.position, 'y', -5, 5)
positionFolder.add(cube.position, 'z', -5, 5)
positionFolder.open()

const rotationFolder = cubeFolder.addFolder('Rotation')
rotationFolder.add(cube.rotation, 'x', 0, Math.PI * 2)
rotationFolder.add(cube.rotation, 'y', 0, Math.PI * 2)
rotationFolder.add(cube.rotation, 'z', 0, Math.PI * 2)
rotationFolder.open()

const scaleFolder = cubeFolder.addFolder('Scale')
scaleFolder.add(cube.scale, 'x', -5, 5)
scaleFolder.add(cube.scale, 'y', -5, 5)
scaleFolder.add(cube.scale, 'z', -5, 5)
scaleFolder.open()

function animate() {
    requestAnimationFrame(animate)

    renderer.render(scene, camera)

    stats.update()
}

animate()
```

### List of Classes that Extend Object3D

A list of Three.js Classes that extend from the [Object3D](https://threejs.org/docs/?q=object#api/en/core/Object3D) base class are,

- [Scene](https://threejs.org/docs/#api/en/scenes/Scene)
- [Mesh](https://threejs.org/docs/#api/en/objects/Mesh)
  - [Instanced Mesh](https://threejs.org/docs/#api/en/objects/InstancedMesh)
  - [Skinned Mesh](https://threejs.org/docs/#api/en/objects/SkinnedMesh)
- [Camera](https://threejs.org/docs/#api/en/cameras/Camera)
  - [Orthographic Camera](https://threejs.org/docs/#api/en/cameras/OrthographicCamera)
  - [Perspective Camera](https://threejs.org/docs/#api/en/cameras/PerspectiveCamera)
- [Cube Camera](https://threejs.org/docs/#api/en/cameras/CubeCamera)
- [Group](https://threejs.org/docs/#api/en/objects/Group)
- [Sprite](https://threejs.org/docs/#api/en/objects/Sprite)
- [LOD](https://threejs.org/docs/#api/en/objects/LOD)
- [Bone](https://threejs.org/docs/#api/en/objects/Bone)
- [Line](https://threejs.org/docs/#api/en/objects/Line)
  - [LineLoop](https://threejs.org/docs/#api/en/objects/LineLoop)
  - [LineSegments](https://threejs.org/docs/#api/en/objects/LineSegments)
- [Points](https://threejs.org/docs/#api/en/objects/Points)
- [Light](https://threejs.org/docs/#api/en/lights/Light)
  - [AmbientLight](https://threejs.org/docs/#api/en/lights/AmbientLight)
  - [DirectionLight](https://threejs.org/docs/#api/en/lights/DirectionalLight)
  - [HemisphereLight](https://threejs.org/docs/#api/en/lights/HemisphereLight)
  - [PointLight](https://threejs.org/docs/#api/en/lights/PointLight)
  - [RectAreaLight](https://threejs.org/docs/#api/en/lights/RectAreaLight)
  - [SpotLight](https://threejs.org/docs/#api/en/lights/SpotLight)
- [AudioListener](https://threejs.org/docs/#api/en/audio/AudioListener)
- [Audio](https://threejs.org/docs/#api/en/audio/Audio)
  - [PositionalAudio](https://threejs.org/docs/#api/en/audio/PositionalAudio)

Some of the above classes are also derived from parent classes that derive from the Object3D.

[9. Object3D Hierarchy](object3dhierarchy.md)
