# Lights

## Description

Let's add a few different types of lights to the scene, and see how they affect the different materials. 

In summary,

- **AmbientLight** illuminates the whole scene in all directions.
- **DirectionalLight** illuminates the whole scene in 1 direction.
- **PointLight** illuminates in all directions from a 3D position. Distance and decay can be managed.
- **SpotLight** illuminates in 1 direction from a 3D position. Distance, decay, angle, penumbra and target can be managed.

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

const plane = new THREE.Mesh(
  new THREE.PlaneGeometry(10, 10),
  new THREE.MeshStandardMaterial()
)
plane.rotation.x = -Math.PI / 2
scene.add(plane)

const data = { color: 0x00ff00, lightColor: 0xffffff }

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

const gui = new GUI()

// #region AmbientLight

const ambientLight = new THREE.AmbientLight(data.lightColor, Math.PI)
ambientLight.visible = false
scene.add(ambientLight)

const ambientLightFolder = gui.addFolder('AmbientLight')
ambientLightFolder.add(ambientLight, 'visible')
ambientLightFolder.addColor(data, 'lightColor').onChange(() => {
  ambientLight.color.set(data.lightColor)
})
ambientLightFolder.add(ambientLight, 'intensity', 0, Math.PI)

// #endregion

// #region DirectionalLight

const directionalLight = new THREE.DirectionalLight(data.lightColor, Math.PI)
directionalLight.position.set(1, 1, 1)
scene.add(directionalLight)

const directionalLightHelper = new THREE.DirectionalLightHelper(
  directionalLight
)
directionalLightHelper.visible = false
scene.add(directionalLightHelper)

const directionalLightFolder = gui.addFolder('DirectionalLight')
directionalLightFolder.add(directionalLight, 'visible')
directionalLightFolder.addColor(data, 'lightColor').onChange(() => {
  directionalLight.color.set(data.lightColor)
})
directionalLightFolder.add(directionalLight, 'intensity', 0, Math.PI * 10)

const directionalLightFolderControls =
  directionalLightFolder.addFolder('Controls')
directionalLightFolderControls
  .add(directionalLight.position, 'x', -1, 1, 0.001)
  .onChange(() => {
    directionalLightHelper.update()
  })
directionalLightFolderControls
  .add(directionalLight.position, 'y', -1, 1, 0.001)
  .onChange(() => {
    directionalLightHelper.update()
  })
directionalLightFolderControls
  .add(directionalLight.position, 'z', -1, 1, 0.001)
  .onChange(() => {
    directionalLightHelper.update()
  })
directionalLightFolderControls
  .add(directionalLightHelper, 'visible')
  .name('Helper Visible')
directionalLightFolderControls.close()

// #endregion

// #region Pointlight

const pointLight = new THREE.PointLight(data.lightColor, Math.PI)
pointLight.position.set(2, 0, 0)
pointLight.visible = false
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

const pointLightFolderControls = pointLightFolder.addFolder('Controls')
pointLightFolderControls.add(pointLight.position, 'x', -10, 10)
pointLightFolderControls.add(pointLight.position, 'y', -10, 10)
pointLightFolderControls.add(pointLight.position, 'z', -10, 10)
pointLightFolderControls.add(pointLight, 'distance', 0, 20).onChange(() => {
  spotLightHelper.update()
})
pointLightFolderControls.add(pointLight, 'decay', 0, 10).onChange(() => {
  spotLightHelper.update()
})
pointLightFolderControls.add(pointLightHelper, 'visible').name('Helper Visible')
pointLightFolderControls.close()

// #endregion

// #region Spotlight

const spotLight = new THREE.SpotLight(data.lightColor, Math.PI)
spotLight.position.set(3, 2.5, 1)
spotLight.visible = false
//spotLight.target.position.set(5, 0, -5)
scene.add(spotLight)

const spotLightHelper = new THREE.SpotLightHelper(spotLight)
spotLightHelper.visible = false
scene.add(spotLightHelper)

const spotLightFolder = gui.addFolder('Spotlight')
spotLightFolder.add(spotLight, 'visible')
spotLightFolder.addColor(data, 'lightColor').onChange(() => {
  spotLight.color.set(data.lightColor)
})
spotLightFolder.add(spotLight, 'intensity', 0, Math.PI * 10)

const spotLightFolderControls = spotLightFolder.addFolder('Controls')
spotLightFolderControls.add(spotLight.position, 'x', -10, 10).onChange(() => {
  spotLightHelper.update()
})
spotLightFolderControls.add(spotLight.position, 'y', -10, 10).onChange(() => {
  spotLightHelper.update()
})
spotLightFolderControls.add(spotLight.position, 'z', -10, 10).onChange(() => {
  spotLightHelper.update()
})
spotLightFolderControls.add(spotLight, 'distance', 0, 20).onChange(() => {
  spotLightHelper.update()
})
spotLightFolderControls.add(spotLight, 'decay', 0, 10).onChange(() => {
  spotLightHelper.update()
})
spotLightFolderControls.add(spotLight, 'angle', 0, 1).onChange(() => {
  spotLightHelper.update()
})
spotLightFolderControls.add(spotLight, 'penumbra', 0, 1, 0.001).onChange(() => {
  spotLightHelper.update()
})
spotLightFolderControls.add(spotLightHelper, 'visible').name('Helper Visible')
spotLightFolderControls.close()

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