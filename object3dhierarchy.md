## Object3D Hierarchy

The scene is an [Object3D](object3d.md). You can add other Object3Ds to the scene and they become children of the scene. The scene itself is derived from the Object3D base class.

If you rotate the scene, or scale the scene, or translate its position, it will affect all if its child objects.

You make an Object3D a child of another Object3D by using the parents `add` method.

```ts
scene.add(cube)
```

You can also add Object3Ds to other Object3Ds that are already part of the scene. Any changes to the Object3D such as position, scale, rotation will affect all the children under the same parent equally.

It is possible to create a Hierarchy of Object3Ds by continually adding new objects to any existing objects.

```
scene
    |--object1 (Red Ball)
             |--object2 (Green Ball)
                       |--object3 (Blue Ball)
```

In this example, the blue ball is a child of the green ball, which is a child of the red ball.

Any changes to the red ball will affect the green and blue balls,

Any changes to the green ball, will affect the blue ball, but not its parent, the red ball.

Any changes to the blue ball, have no effect on its parent, or grandparent balls.

Any number of Object3Ds can be added as children of other Object3Ds.

An Object3D can have only 1 parent, and you can change its parent dynamically any time.

Changing an Object3Ds parent will affect the current position, scale and rotation based in the parent it has now become a child of.

```ts
scene.position.set(0, 0, 0) // this is default position of a scene, and any Object3Ds
cube.position.set(5, 0, 0) // cube is offset x = 5 from of its parent.
scene.add(cube) // add the cube to the scene. The scene becomes its parent Object3D.

sphere.position.set(3, 0, 0) // sphere is offset x = 3 from the center of its parent.
cube.add(sphere) // The spheres parent is now the cube. The sphere will be x=3 offset from the cube in local space. So that will be 8 in world space since the cube is a child of the scene and is already offset x = 5.
```

Getting an Object3D transform, such as position, rotation/quaternion and scale will return the values in local transform space. If the Object3D is a direct descendant of the scene, then the world space values will be identical.

If your Object3D is a child of another Object3D which is already a child of the scene, then the world transform values will also consider the transforms of its parent, grandparents, great grandparents etc.

```ts
obj.position // values are local transform
obj.rotation // values are local transform
obj.quaternion // values are local transform
obj.scale // values are local transform
```

To get the world transforms of an object,

```ts
const objectsWorldPosition = new THREE.Vector3()
object.getWorldPosition(objectsWorldPosition)
console.log(objectsWorldPosition)

const objectsWorldDirection = new THREE.Vector3()
object.getWorldDirection(objectsWorldDirection)
console.log(objectsWorldDirection)

const objectsWorldQuaternion = new THREE.Quaternion()
object.getWorldQuaternion(objectsWorldQuaternion)
console.log(objectsWorldQuaternion)

const objectsWorldScale = new THREE.Vector3()
object.getWorldScale(objectsWorldScale)
console.log(objectsWorldScale)
```

## Code

`./index.html`

```diff
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Three.js TypeScript</title>
    </head>

    <body>
+       <div id="debug"></div>
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
#debug {
    font-family: monospace;
    font-size: larger;
    position: absolute;
    left: 0px;
    top: 50px;
    border: 1px solid red;
    padding: 10px;
    width: 180px;
    height: 220px;
    color: white;
    pointer-events: none;
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

const camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000)
camera.position.set(4, 4, 4)

const renderer = new THREE.WebGLRenderer()
renderer.setSize(window.innerWidth, window.innerHeight)
document.body.appendChild(renderer.domElement)

const controls = new OrbitControls(camera, renderer.domElement)
controls.target.set(8, 0, 0)
controls.update()

const light = new THREE.PointLight(0xffffff, 400)
light.position.set(10, 10, 10)
scene.add(light)

const object1 = new THREE.Mesh(new THREE.SphereGeometry(), new THREE.MeshPhongMaterial({ color: 0xff0000 }))
object1.position.set(4, 0, 0)
scene.add(object1)
object1.add(new THREE.AxesHelper(5))

const object2 = new THREE.Mesh(new THREE.SphereGeometry(), new THREE.MeshPhongMaterial({ color: 0x00ff00 }))
object2.position.set(4, 0, 0)
object1.add(object2)
object2.add(new THREE.AxesHelper(5))

const object3 = new THREE.Mesh(new THREE.SphereGeometry(), new THREE.MeshPhongMaterial({ color: 0x0000ff }))
object3.position.set(4, 0, 0)
object2.add(object3)
object3.add(new THREE.AxesHelper(5))

window.addEventListener('resize', () => {
    camera.aspect = window.innerWidth / window.innerHeight
    camera.updateProjectionMatrix()
    renderer.setSize(window.innerWidth, window.innerHeight)
})

const gui = new GUI()
const object1Folder = gui.addFolder('Object1 (Red Ball)')
object1Folder.add(object1.position, 'x', 0, 10, 0.01).name('X Position')
object1Folder.add(object1.rotation, 'x', 0, Math.PI * 2, 0.01).name('X Rotation')
object1Folder.add(object1.scale, 'x', 0, 2, 0.01).name('X Scale')
object1Folder.open()
const object2Folder = gui.addFolder('Object2 (Green Ball)')
object2Folder.add(object2.position, 'x', 0, 10, 0.01).name('X Position')
object2Folder.add(object2.rotation, 'x', 0, Math.PI * 2, 0.01).name('X Rotation')
object2Folder.add(object2.scale, 'x', 0, 2, 0.01).name('X Scale')
object2Folder.open()
const object3Folder = gui.addFolder('Object3 (Blue Ball)')
object3Folder.add(object3.position, 'x', 0, 10, 0.01).name('X Position')
object3Folder.add(object3.rotation, 'x', 0, Math.PI * 2, 0.01).name('X Rotation')
object3Folder.add(object3.scale, 'x', 0, 2, 0.01).name('X Scale')
object3Folder.open()

const stats = new Stats()
document.body.appendChild(stats.dom)

const debug = document.getElementById('debug') as HTMLDivElement

function animate() {
    requestAnimationFrame(animate)

    renderer.render(scene, camera)

    const object1WorldPosition = new THREE.Vector3()
    object1.getWorldPosition(object1WorldPosition)
    const object2WorldPosition = new THREE.Vector3()
    object2.getWorldPosition(object2WorldPosition)
    const object3WorldPosition = new THREE.Vector3()
    object3.getWorldPosition(object3WorldPosition)

    debug.innerText =
        'Red\n' +
        'Local Pos X : ' +
        object1.position.x.toFixed(2) +
        '\n' +
        'World Pos X : ' +
        object1WorldPosition.x.toFixed(2) +
        '\n' +
        '\nGreen\n' +
        'Local Pos X : ' +
        object2.position.x.toFixed(2) +
        '\n' +
        'World Pos X : ' +
        object2WorldPosition.x.toFixed(2) +
        '\n' +
        '\nBlue\n' +
        'Local Pos X : ' +
        object3.position.x.toFixed(2) +
        '\n' +
        'World Pos X : ' +
        object3WorldPosition.x.toFixed(2) +
        '\n'

    stats.update()
}

animate()
```

[10. Geometry](geometry.md)
