# Shadows

## Description

In this lesson we will look at the shadows created by the `SpotLight`, `PointLight` and `DirectionaLight`.

Lights manage their own camera frustums when they are set with `castShadows=true`.

The `PointLight` and `SpotLight` will calculate there shadows using a `PerspectiveCamera` frustum. The defaults are,

| **Property** | **Value** |
| ----- | ----- |
| fov | 90 |
| aspect | 1 |
| near | 0.5 |
| far | 500 |

The `DirectionalLight` shadow will be calculated using an `OrthographicCamera` frustum. This is because light rays from a DirectionalLight are parallel. The defaults are,


| **Property** | **Value** |
| ----- | ------ |
| left | -5 |
| right |	5 |
| top |	5 |
| bottom |	-5 |
| near |	0.5 |
| far |	500 |

When using shadows, there are several extra considerations when creating your scene.

You need to enable the `shadowMap` property in your renderer.

E.g.,

```ts
renderer.shadowMap.enabled = true
```

You can optionally set the `shadowMap.type`.

E.g.,

```ts
renderer.shadowMap.type = THREE.PCFShadowMap // (default)
```

Possible `shadowMap.type` values are,

| **Type** | **Value** |
| ----- | ----- |
| BasicShadowMap |	gives unfiltered shadow maps - fastest, but lowest quality. |
| PCFShadowMap |	(Default) filters shadow maps using the Percentage-Closer Filtering (PCF) algorithm (default). |
| PCFSoftShadowMap |	filters shadow maps using the Percentage-Closer Soft Shadows (PCSS) algorithm. |
| VSMShadowMap |	filters shadow maps using the Variance Shadow Map (VSM) algorithm. When using VSMShadowMap all shadow receivers will also cast shadows. |

Your lights that you want to cast shadows will need to have their castShadow property set to true.

E.g.,

```ts
directionalLight.castShadow = true
pointLight.castShadow = true
spotLight.castShadow = true
```

Your scene meshes will need either or both the `castShadow` and `receiveShadow` properties set to `true`.

```ts
box.castShadow = true
ground.receiveShadow = true
```

Note that when a mesh has both its `castShadow` and `receiveShadow` properties set to true, it can cause artefacts or black lines to appear on the surface. This can be adjusted using the shadows `bias` property. Experiment with small amounts such as `0.1`, `-0.0001`, `0.002`. The default is `0`.

## Code

`./index.html`

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
}
```

`./src/main.ts`

```ts
import './style.css'
import * as THREE from 'three'
import { OrbitControls } from 'three/addons/controls/OrbitControls.js'
import Stats from 'three/addons/libs/stats.module.js'
import { GUI } from 'three/addons/libs/lil-gui.module.min.js'

const scene = new THREE.Scene()

scene.add(new THREE.GridHelper())

const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 100)
camera.position.set(-1, 4, 2.5)

const renderer = new THREE.WebGLRenderer({ antialias: true })
renderer.shadowMap.type = THREE.PCFShadowMap // (default)
//renderer.shadowMap.type = THREE.PCFSoftShadowMap
//renderer.shadowMap.type = THREE.BasicShadowMap
//renderer.shadowMap.type = THREE.VSMShadowMap
renderer.shadowMap.enabled = true
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

window.addEventListener('resize', () => {
  camera.aspect = window.innerWidth / window.innerHeight
  camera.updateProjectionMatrix()
  renderer.setSize(window.innerWidth, window.innerHeight)
})

const controls = new OrbitControls(camera, renderer.domElement)
controls.enableDamping = true

const plane = new THREE.Mesh(new THREE.PlaneGeometry(100, 100), new THREE.MeshStandardMaterial({ color: 0xffffff }))
plane.rotation.x = -Math.PI / 2
plane.receiveShadow = true
plane.castShadow = true
scene.add(plane)

const data = {
  color: 0x00ff00,
  lightColor: 0xffffff,
  shadowMapSizeWidth: 512,
  shadowMapSizeHeight: 512,
}

const geometry = new THREE.IcosahedronGeometry(1, 1)

const meshes = [
  new THREE.Mesh(geometry, new THREE.MeshBasicMaterial({ color: data.color })),
  new THREE.Mesh(geometry, new THREE.MeshNormalMaterial({ flatShading: true })),
  new THREE.Mesh(geometry, new THREE.MeshPhongMaterial({ color: data.color, flatShading: true })),
  new THREE.Mesh(geometry, new THREE.MeshStandardMaterial({ color: data.color, flatShading: true })),
]

meshes[0].position.set(-3, 1, 0)
meshes[1].position.set(-1, 1, 0)
meshes[2].position.set(1, 1, 0)
meshes[3].position.set(3, 1, 0)

meshes[0].castShadow = true
meshes[1].castShadow = true
meshes[2].castShadow = true
meshes[3].castShadow = true
//meshes.map((m) => (m.castShadow = true)) // using array map
meshes.map((m) => (m.receiveShadow = true))

// and instead of using `meshes.map` on two lines in a row, you can use once and share it like,
// meshes.map((m) => {
//   m.castShadow = true
//   m.receiveShadow = true
// })

scene.add(...meshes)

const gui = new GUI()

// #region DirectionalLight

const directionalLight = new THREE.DirectionalLight(data.lightColor, Math.PI)
directionalLight.position.set(1, 1, 1)
directionalLight.castShadow = true
directionalLight.shadow.camera.near = 0
directionalLight.shadow.camera.far = 10
directionalLight.shadow.mapSize.width = data.shadowMapSizeWidth
directionalLight.shadow.mapSize.height = data.shadowMapSizeHeight
scene.add(directionalLight)

// const directionalLightHelper = new THREE.DirectionalLightHelper(directionalLight)
const directionalLightHelper = new THREE.CameraHelper(directionalLight.shadow.camera)
directionalLightHelper.visible = false
scene.add(directionalLightHelper)

const directionalLightFolder = gui.addFolder('DirectionalLight')
directionalLightFolder.add(directionalLight, 'visible')
directionalLightFolder.addColor(data, 'lightColor').onChange(() => {
  directionalLight.color.set(data.lightColor)
})
directionalLightFolder.add(directionalLight, 'intensity', 0, Math.PI * 10)
directionalLightFolder.add(directionalLight.position, 'x', -5, 5, 0.001).onChange(() => {
  directionalLightHelper.update()
})
directionalLightFolder.add(directionalLight.position, 'y', -5, 5, 0.001).onChange(() => {
  directionalLightHelper.update()
})
directionalLightFolder.add(directionalLight.position, 'z', -5, 5, 0.001).onChange(() => {
  directionalLightHelper.update()
})
directionalLightFolder.add(directionalLightHelper, 'visible').name('Helper Visible')
directionalLightFolder.add(directionalLight.shadow.camera, 'left', -10, -1, 0.1).onChange(() => {
  directionalLight.shadow.camera.updateProjectionMatrix()
  directionalLightHelper.update()
})
directionalLightFolder.add(directionalLight.shadow.camera, 'right', 1, 10, 0.1).onChange(() => {
  directionalLight.shadow.camera.updateProjectionMatrix()
  directionalLightHelper.update()
})
directionalLightFolder.add(directionalLight.shadow.camera, 'top', 1, 10, 0.1).onChange(() => {
  directionalLight.shadow.camera.updateProjectionMatrix()
  directionalLightHelper.update()
})
directionalLightFolder.add(directionalLight.shadow.camera, 'bottom', -10, -1, 0.1).onChange(() => {
  directionalLight.shadow.camera.updateProjectionMatrix()
  directionalLightHelper.update()
})
directionalLightFolder.add(directionalLight.shadow.camera, 'near', 0, 100).onChange(() => {
  directionalLight.shadow.camera.updateProjectionMatrix()
  directionalLightHelper.update()
})
directionalLightFolder.add(directionalLight.shadow.camera, 'far', 0.1, 100).onChange(() => {
  directionalLight.shadow.camera.updateProjectionMatrix()
  directionalLightHelper.update()
})
directionalLightFolder.add(data, 'shadowMapSizeWidth', [256, 512, 1024, 2048, 4096]).onChange(() => updateDirectionalLightShadowMapSize())
directionalLightFolder.add(data, 'shadowMapSizeHeight', [256, 512, 1024, 2048, 4096]).onChange(() => updateDirectionalLightShadowMapSize())
directionalLightFolder.add(directionalLight.shadow, 'radius', 1, 10, 1).name('radius (PCF | VSM)') // PCFShadowMap or VSMShadowMap
directionalLightFolder.add(directionalLight.shadow, 'blurSamples', 1, 20, 1).name('blurSamples (VSM)') // VSMShadowMap only
directionalLightFolder.open()

function updateDirectionalLightShadowMapSize() {
  directionalLight.shadow.mapSize.width = data.shadowMapSizeWidth
  directionalLight.shadow.mapSize.height = data.shadowMapSizeHeight
  directionalLight.shadow.map = null
}
// #endregion

// #region Pointlight

const pointLight = new THREE.PointLight(data.lightColor, Math.PI)
pointLight.position.set(2, 1, 0)
pointLight.visible = false
pointLight.castShadow = true
scene.add(pointLight)

const pointLightHelper = new THREE.PointLightHelper(pointLight)
pointLightHelper.visible = false
scene.add(pointLightHelper)

const pointLightFolder = gui.addFolder('Pointlight')
pointLightFolder.add(pointLight, 'visible')
pointLightFolder.addColor(data, 'lightColor').onChange(() => {
  pointLight.color.set(data.lightColor)
})
pointLightFolder.add(pointLight, 'intensity', 0, Math.PI * 10)
pointLightFolder.add(pointLight.position, 'x', -10, 10)
pointLightFolder.add(pointLight.position, 'y', -10, 10)
pointLightFolder.add(pointLight.position, 'z', -10, 10)
pointLightFolder.add(pointLight, 'distance', 0.01, 20)
pointLightFolder.add(pointLight, 'decay', 0, 10)
pointLightFolder.add(pointLightHelper, 'visible').name('Helper Visible')
pointLightFolder.add(pointLight.shadow.camera, 'near', 0.01, 100).onChange(() => {
  pointLight.shadow.camera.updateProjectionMatrix()
  pointLightHelper.update()
})
pointLightFolder.add(pointLight.shadow.camera, 'far', 0.1, 100).onChange(() => {
  pointLight.shadow.camera.updateProjectionMatrix()
  pointLightHelper.update()
})
pointLightFolder.add(data, 'shadowMapSizeWidth', [256, 512, 1024, 2048, 4096]).onChange(() => updatePointLightShadowMapSize())
pointLightFolder.add(data, 'shadowMapSizeHeight', [256, 512, 1024, 2048, 4096]).onChange(() => updatePointLightShadowMapSize())
pointLightFolder.add(pointLight.shadow, 'radius', 1, 10, 1).name('radius (PCF | VSM)') // PCFShadowMap or VSMShadowMap
pointLightFolder.add(pointLight.shadow, 'blurSamples', 1, 20, 1).name('blurSamples (VSM)') // VSMShadowMap only
pointLightFolder.close()

function updatePointLightShadowMapSize() {
  pointLight.shadow.mapSize.width = data.shadowMapSizeWidth
  pointLight.shadow.mapSize.height = data.shadowMapSizeHeight
  pointLight.shadow.map = null
}

// #endregion

// #region Spotlight

const spotLight = new THREE.SpotLight(data.lightColor, Math.PI)
spotLight.position.set(3, 2.5, 1)
spotLight.visible = false
//spotLight.target.position.set(5, 0, -5)
spotLight.castShadow = true
scene.add(spotLight)

//const spotLightHelper = new THREE.SpotLightHelper(spotLight)
const spotLightHelper = new THREE.CameraHelper(spotLight.shadow.camera)
spotLightHelper.visible = false
scene.add(spotLightHelper)

const spotLightFolder = gui.addFolder('Spotlight')
spotLightFolder.add(spotLight, 'visible')
spotLightFolder.addColor(data, 'lightColor').onChange(() => {
  spotLight.color.set(data.lightColor)
})
spotLightFolder.add(spotLight, 'intensity', 0, Math.PI * 10)
spotLightFolder.add(spotLight.position, 'x', -10, 10).onChange(() => {
  spotLight.shadow.camera.updateProjectionMatrix()
  spotLightHelper.update()
})
spotLightFolder.add(spotLight.position, 'y', -10, 10).onChange(() => {
  spotLight.shadow.camera.updateProjectionMatrix()
  spotLightHelper.update()
})
spotLightFolder.add(spotLight.position, 'z', -10, 10).onChange(() => {
  spotLight.shadow.camera.updateProjectionMatrix()
  spotLightHelper.update()
})
spotLightFolder.add(spotLight, 'distance', 0.01, 100).onChange(() => {
  spotLight.shadow.camera.updateProjectionMatrix()
  spotLightHelper.update()
})
spotLightFolder.add(spotLight, 'decay', 0, 10).onChange(() => {
  spotLight.shadow.camera.updateProjectionMatrix()
  spotLightHelper.update()
})
spotLightFolder.add(spotLight, 'angle', 0, 1).onChange(() => {
  spotLight.shadow.camera.updateProjectionMatrix()
  spotLightHelper.update()
})
spotLightFolder.add(spotLight, 'penumbra', 0, 1, 0.001).onChange(() => {
  spotLight.shadow.camera.updateProjectionMatrix()
  spotLightHelper.update()
})
spotLightFolder.add(spotLightHelper, 'visible').name('Helper Visible')
spotLightFolder.add(spotLight.shadow.camera, 'near', 0.01, 100).onChange(() => {
  spotLight.shadow.camera.updateProjectionMatrix()
  spotLightHelper.update()
})
spotLightFolder.add(data, 'shadowMapSizeWidth', [256, 512, 1024, 2048, 4096]).onChange(() => updateSpotLightShadowMapSize())
spotLightFolder.add(data, 'shadowMapSizeHeight', [256, 512, 1024, 2048, 4096]).onChange(() => updateSpotLightShadowMapSize())
spotLightFolder.add(spotLight.shadow, 'radius', 1, 10, 1).name('radius (PCF | VSM)') // PCFShadowMap or VSMShadowMap
spotLightFolder.add(spotLight.shadow, 'blurSamples', 1, 20, 1).name('blurSamples (VSM)') // VSMShadowMap only
spotLightFolder.close()

function updateSpotLightShadowMapSize() {
  spotLight.shadow.mapSize.width = data.shadowMapSizeWidth
  spotLight.shadow.mapSize.height = data.shadowMapSizeHeight
  spotLight.shadow.map = null
}

// #endregion

const stats = new Stats()
document.body.appendChild(stats.dom)

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
  }

  renderer.render(scene, camera)

  stats.update()
}

animate()
```

## References

[DirectionalLightShadow](https://threejs.org/docs/?q=direction#api/en/lights/shadows/DirectionalLightShadow)

[PointLightShadow](https://threejs.org/docs/?q=pointlightsh#api/en/lights/shadows/PointLightShadow)

[SpotLightShadow](https://threejs.org/docs/#api/en/lights/shadows/SpotLightShadow)
