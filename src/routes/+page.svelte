<script lang="ts">
import {
    onMount,
    onDestroy
} from 'svelte';
import * as PIXI from 'pixi.js';

let app: PIXI.Application;
let timelineContainer: PIXI.Container;

onMount(async () => {
    // Création de l'application Pixi
    app = new PIXI.Application();
    const container = document.getElementById('pixi-container');
    if (container) {
        await app.init({
            antialias: true,
            resizeTo: container,
            backgroundColor: 0x303030
        });
        // Ajout du canvas au DOM
        container.appendChild(app.canvas);
        // Conteneur pour la timeline
        timelineContainer = new PIXI.Container();
        app.stage.addChild(timelineContainer);

        // Dessiner la timeline
        drawTimeline();
    }

});

const drawTimeline = () => {
    // Dimensions de la timeline
    const trackHeight = 50;
    const trackPadding = 10;

    // Exemple de clips à afficher
    const clips = [{
            start: 0,
            duration: 5,
            color: 0xff0000
        },
        {
            start: 6,
            duration: 3,
            color: 0x00ff00
        },
        {
            start: 10,
            duration: 7,
            color: 0x0000ff
        },
    ];

    // Dessiner les pistes
    clips.forEach((clip, index) => {
        const trackY = index * (trackHeight + trackPadding);

        const clipGraphics = new PIXI.Graphics();
        clipGraphics.roundRect(clip.start * 50, trackY, clip.duration * 50, trackHeight,5);
        clipGraphics.fill(clip.color);

        // Ajouter le clip au conteneur de la timeline
        timelineContainer.addChild(clipGraphics);

        // Ajouter le texte de durée sur le clip
        const durationText = new PIXI.Text(`${clip.duration}s`, {
            fontSize: 14,
            fill: 0xffffff
        });
        durationText.position.set(clip.start * 50 + 10, trackY + (trackHeight / 2) - 10);
        timelineContainer.addChild(durationText);
    });

    // Ajouter des marqueurs de temps (toutes les secondes par exemple)
    for (let i = 0; i <= 20; i++) {
        const marker = new PIXI.Graphics();
        // marker.setStrokeStyle();
        marker.moveTo(i * 50, 0);
        marker.lineTo(i * 50, 300);
        marker.stroke({
            color: 0xffffff,
            width: 0.3,
        })
		
        timelineContainer.addChild(marker);

        // Ajouter le texte des marqueurs
        const timeText = new PIXI.Text(`${i}s`, {
            fontSize: 12,
            fill: 0xffffff
        });
        timeText.position.set(i * 50 + 5,  5);
        timelineContainer.addChild(timeText);
    }
};

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
    position: absolute;
    bottom: 0px;
    width: 100%;
    height: 300px;
    display: flex;
    justify-content: center;
    align-items: center;
    /* height: 100vh; */
}
</style>

<a href="/konva">Konva</a>
<a href="/three">three</a>

<div id="pixi-container"></div>
