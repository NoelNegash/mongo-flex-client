<script lang="ts">

    import { onMount, onDestroy } from 'svelte';
    import * as THREE from 'three';
    
    export let house;
    export let size = 300;

    let el;



    let scene = new THREE.Scene();
    let camera = new THREE.PerspectiveCamera(75, 1, 0.1, 1000);
    let geometry = new THREE.BoxGeometry();
    let cubes = []
    let renderer;

    for (let i = 0; i < house.gridPieces.length; i++) {
        let p = house.gridPieces[i]
        let offset = (p[1]%house.size)*3
        let cube = new THREE.Mesh(geometry, new THREE.MeshBasicMaterial({
            color: "rgb("+
            house.colors[offset] + ", " + 
            house.colors[offset+1] + ", " + 
            house.colors[offset+2] + ")"
        }))
        cube.position.x = p[0]
        cube.position.y = p[1]
        cube.position.z = p[2]
        scene.add(cube)
        cubes.push(cube)
    } 

    let t = 0, rad = 7

    const animate = () => {
        if (camera == null) return
        requestAnimationFrame(animate);
        camera.position.y = 2
        camera.position.x = Math.cos(t)*rad
        camera.position.z = Math.sin(t)*rad

        camera.lookAt(new THREE.Vector3(0,0,0))
        
        renderer.render(scene, camera);

        t+=0.01
    };

    const resize = () => {
        renderer.setSize(size, size)
        camera.aspect = window.innerWidth / window.innerHeight;
        camera.updateProjectionMatrix();
    };

    export const createScene = (el) => {
        renderer = new THREE.WebGLRenderer({ antialias: true, canvas: el });
        resize();
        animate();
    }

    window.addEventListener('resize', resize);
    onMount(() => {
        createScene(el)
    })
    onDestroy(() => {
        camera = null
        geometry = null
        cubes = null
        renderer = null
        scene = null

    })
</script>

<canvas bind:this={el}></canvas>

