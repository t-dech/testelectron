<script lang="ts">
import {
    onMount, onDestroy
} from 'svelte';
import * as PIXI from 'pixi.js';

let app: PIXI.Application;

onMount(async () => {
    // Création de l'application Pixi
    app = new PIXI.Application();
    await app.init({ antialias: true, resizeTo: window });
    // Ajout du canvas au DOM
    const container = document.getElementById('pixi-container');
    if (container) {
        container.appendChild(app.canvas);
    }

    // Création d'un rectangle
    const graphics = new PIXI.Graphics();
    graphics.rect(50, 50, 100, 100);
    graphics.fill(0xde3249);
	// Opt-in to interactivity
    graphics.eventMode = 'static';

    // Shows hand cursor
    graphics.cursor = 'pointer';
	graphics.on('pointerdown', onClick);
    // Ajout du rectangle à la scène Pixi
    app.stage.addChild(graphics);

	function onClick()
    {
        graphics.scale.x *= 1.25;
        graphics.scale.y *= 1.25;
    }
});

// Nettoyage lors de la destruction du composant
onDestroy(() => {
    if (app) {
        app.destroy(true, {
            children: true
        });
    }
});
</script>

<style>
 #pixi-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;}

</style>

<div id="pixi-container"></div>
