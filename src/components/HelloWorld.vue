<template>
  <div id="container"></div>
  <scene></scene>
</template>

<script setup>
  import { defineProps, reactive } from "vue";
  import * as THREE from "three";

  defineProps({
    msg: String,
  });

  var lines1 = [
    [-1, 0, 0],
    [0, 0, 0],
    [0, 4, 0],
    [2, 0, 0],
    [3, 0, 0],
    [3, 6, 0],
    [2, 6, 0],
    [2, 2, 0],
    [0, 6, 0],
    [-1, 6, 0],
    [-1, 0, 0],
  ];
  // Setting up canvas
  const scene = new THREE.Scene();
  const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 1, 500);
  const renderer = new THREE.WebGLRenderer();
  const loader = new THREE.TextureLoader();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.body.appendChild(renderer.domElement);

  // set up 3d plane
  const planeSize = 40;
  const texture = loader.load("src/assets/checker.png");
  texture.wrapS = THREE.RepeatWrapping;
  texture.wrapT = THREE.RepeatWrapping;
  texture.magFilter = THREE.NearestFilter;
  const repeats = planeSize / 2;
  texture.repeat.set(repeats, repeats);
  const planeGeo = new THREE.PlaneBufferGeometry(planeSize, planeSize);
  const planeMat = new THREE.MeshPhongMaterial({
    map: texture,
    side: THREE.DoubleSide,
  });
  const mesh = new THREE.Mesh(planeGeo, planeMat);
  mesh.rotation.x = Math.PI * -0.5;
  scene.add(mesh);

  // camera set up
  camera.position.set(0, 5, 10);
  camera.lookAt(0, 0, 0);

  // add an orbitor
  import { OrbitControls } from "three/examples/jsm/controls/OrbitControls";
  const controls = new OrbitControls(camera, renderer.domElement);
  controls.target.set(0, 2, 0);
  controls.update();

  // adding a light
  const light = new THREE.DirectionalLight(0x330ff, 1);
  const light2 = new THREE.DirectionalLight(0xe1931a, 4);
  // const light3 = new THREE.DirectionalLight(0xffffff, 2);
  const ambientLight = new THREE.AmbientLight(0xffffff, 1);
  light.position.set(6, 4, 3);
  light2.position.set(-3, -2, -3);
  // light3.position.set(0, 2, 5);
  scene.add(light);
  scene.add(light2);
  scene.add(ambientLight);
  // scene.add(light3);

  // adding a texture
  const materialPic = new THREE.MeshBasicMaterial({
    map: loader.load("src/assets/wall.jpg"),
  });

  // box geometery
  const geometry = new THREE.BoxGeometry(1, 1, 1);
  const cubes = [makeInstance(geometry, 0x44aa88, 0), makeInstance(geometry, 0x8844aa, -2, materialPic), makeInstance(geometry, 0xaa8844, 2)];
  function makeInstance(geometry, color, x, mat) {
    if (!mat) {
      const material = new THREE.MeshPhongMaterial({ color });
      const cube = new THREE.Mesh(geometry, material);
      scene.add(cube);
      cube.position.x = x;
      cube.position.y = +3;
      return cube;
    } else {
      const cube = new THREE.Mesh(geometry, mat);
      scene.add(cube);
      cube.position.x = x;
      cube.position.y = +3;
      return cube;
    }
  }
  cubes[0].position.z = 2;
  cubes[2].position.z = -2;

  renderLines(lines1, 0x44aa88, 3);
  renderLines(lines1, 0x44aa88, 3, [0, 0, 2]);

  function renderLines(pointArray, colorRef, yMove, arrayShift) {
    // arrayShift will move the x,y,z cordinates if an array is present
    if (arrayShift) {
      arrayShift.forEach((x, index) => {
        if (x) {
          pointArray.forEach((v) => (v[index] += x));
        }
      });
    }

    const lineMaterial = new THREE.LineBasicMaterial({ color: colorRef });
    const points = [];
    points.sort;
    pointArray.forEach((x) => points.push(new THREE.Vector3(x[0], x[1], x[2])));
    const lineGeometry = new THREE.BufferGeometry().setFromPoints(points);
    const line = new THREE.Line(lineGeometry, lineMaterial);
    // fourCorners(pointArray);
    scene.add(line);
    line.translateY(yMove);
  }

  // function fourCorners(points) {
  //   // find lowest x and highest y
  //   points.sort((a[0], b[0]) => b[0] - a);

  // }

  function render(time) {
    time *= 0.001; // convert time to seconds

    cubes.forEach((cube, ndx) => {
      const speed = 1 + ndx * 0.1;
      const rot = time * speed;
      cube.rotation.x = rot;
      cube.rotation.y = rot;
    });
    renderer.render(scene, camera);
    requestAnimationFrame(render);
  }
  requestAnimationFrame(render);
</script>

<style scoped>
  a {
    color: #42b983;
  }
</style>
