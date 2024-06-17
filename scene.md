Before we can see anything with Three.js, we need to consider at least 3 things,

1. Scene
2. Camera
3. Renderer

In the next 3 lessons, we will quickly introduce each of these concepts.

## Scene

A **Scene** allows you to set up, in 3D coordinates, what is to be rendered by Three.js.
We need to have a scene. The scene is also sometimes referred to as the scene graph.
To introduce the scene, we will experiment with the background property.
The default colour of a scene is null. In that case it will use the default clear colour of the renderer.

If we change the scenes background colour, the renderer will then use that colour instead.

```diff
const scene = new THREE.Scene()
+ scene.background = new THREE.Color(0x123456)
```

Or we could set an image.

```diff
const scene = new THREE.Scene()
+ scene.background = new THREE.TextureLoader().load('https://sbcode.net/img/grid.png')
```

Or a cube map consisting of 6 images. This can also be known as a sky box.

```diff
const scene = new THREE.Scene()
+ scene.background = new THREE.CubeTextureLoader().setPath('https://sbcode.net/img/').load(['px.png', 'nx.png', 'py.png', 'ny.png', 'pz.png', 'nz.png'])
```

And we can also blur the cube map to make it appear as a gradient.

```ts
scene.backgroundBlurriness = 0.5
```

We can have multiple scenes. In the code below, I have created 3 independent scenes using the above background examples, and added buttons to the GUI to allow switching between them.

### Script

`./src/main.ts`

```ts
import './style.css'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import Stats from 'three/addons/libs/stats.module.js'
import { GUI } from 'lil-gui'

const sceneA = new THREE.Scene()
sceneA.background = new THREE.Color(0x123456)

const sceneB = new THREE.Scene()
sceneB.background = new THREE.TextureLoader().load(
  'https://sbcode.net/img/grid.png'
)

const sceneC = new THREE.Scene()
sceneC.background = new THREE.CubeTextureLoader()
  .setPath('https://sbcode.net/img/')
  .load(['px.png', 'nx.png', 'py.png', 'ny.png', 'pz.png', 'nz.png'])
//sceneC.backgroundBlurriness = 0.5

const camera = new THREE.PerspectiveCamera(
  75,
  window.innerWidth / window.innerHeight,
  0.1,
  1000
)
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
sceneA.add(cube)
//sceneC.add(cube)

const stats = new Stats()
document.body.appendChild(stats.dom)

let activeScene = sceneA
const setScene = {
  sceneA: () => {
    activeScene = sceneA
  },
  sceneB: () => {
    activeScene = sceneB
  },
  sceneC: () => {
    activeScene = sceneC
  },
}

const gui = new GUI()
gui.add(setScene, 'sceneA').name('Scene A')
gui.add(setScene, 'sceneB').name('Scene B')
gui.add(setScene, 'sceneC').name('Scene C')

function animate() {
  requestAnimationFrame(animate)

  //cube.rotation.x += 0.01
  //cube.rotation.y += 0.01

  renderer.render(activeScene, camera)

  stats.update()
}

animate()
```

In the above code, buttons are added to the GUI. The GUI will use a different control, depending on the targets' property initial value.

- `int/float` ⇒ will draw a slider
- `boolean` ⇒ will add a checkbox
- `string` ⇒ will add a text input
- `function` ⇒ will add a button

Each buttons target is an object, whose property is one of the functions that set which scene is to become active when pressed. See lines 41-51 in the above code.
