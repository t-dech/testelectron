<script lang="ts">
    import { onMount } from 'svelte';
    import * as THREE from 'three';
    import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader';
    import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls';

    let container: HTMLDivElement; // Conteneur pour le canevas Three.js

    onMount(() => {
        // Initialiser la scène, la caméra et le renderer
        const scene = new THREE.Scene();
        const camera = new THREE.PerspectiveCamera(75, container.clientWidth / container.clientHeight, 0.1, 1000);
        const renderer = new THREE.WebGLRenderer({ antialias: true });

        // Configuration du renderer
        renderer.setSize(container.clientWidth, container.clientHeight);
        container.appendChild(renderer.domElement);

        // Ajouter de la lumière
        const light = new THREE.DirectionalLight(0xffffff, 1);
        light.position.set(5, 10, 7.5).normalize();
        scene.add(light);

        // Charger le fichier GLB
        const loader = new GLTFLoader();
        loader.load('test.glb', (gltf: { scene: THREE.Object3D<THREE.Object3DEventMap>; }) => {
            scene.add(gltf.scene);

            // Optionnel : centrer le modèle
            const box = new THREE.Box3().setFromObject(gltf.scene);
            const center = box.getCenter(new THREE.Vector3());
            gltf.scene.position.sub(center); // Centre le modèle à (0, 0, 0)

            // Optionnel : ajuster la taille du modèle
            const size = box.getSize(new THREE.Vector3()).length();
            const scale = 10 / size; // Ajuster l'échelle pour que le modèle ait une taille standardisée
            gltf.scene.scale.set(scale, scale, scale);
        });

        // Positionner la caméra
        camera.position.z = 5;

        // Ajouter OrbitControls pour permettre la rotation, le pan, et le zoom
        const controls = new OrbitControls(camera, renderer.domElement);
        controls.enableDamping = true; // Améliore le mouvement fluide
        controls.dampingFactor = 0.05;
        controls.screenSpacePanning = false; // Bloquer le déplacement de la caméra en profondeur
        controls.maxPolarAngle = Math.PI / 2; // Bloquer l'angle de la caméra pour qu'elle ne passe pas sous le modèle

        // Fonction d'animation
        const animate = () => {
            requestAnimationFrame(animate);
            controls.update(); // Nécessaire si enableDamping ou autoRotate est activé
            renderer.render(scene, camera);
        };

        animate(); // Démarrer l'animation

        // Ajuster le renderer en cas de redimensionnement
        window.addEventListener('resize', () => {
            const width = container.clientWidth;
            const height = container.clientHeight;
            renderer.setSize(width, height);
            camera.aspect = width / height;
            camera.updateProjectionMatrix();
        });
    });
</script>

<style>
    :global(body) {
        margin: 0;
    }

    #three-container {
        width: 100%;
        height: 100vh;
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: #000;
    }
</style>

<div id="three-container" bind:this={container}></div>
