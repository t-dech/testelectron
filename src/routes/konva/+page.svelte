<script lang="ts">
import {
    onMount,
    onDestroy
} from 'svelte';
import Konva from 'konva';

let stage: Konva.Stage;
let layer: Konva.Layer;
const width = 1000;
const height = 300;

const clips = [{
        start: 0,
        duration: 5,
        color: 'red'
    },
    {
        start: 6,
        duration: 3,
        color: 'green'
    },
    {
        start: 10,
        duration: 7,
        color: 'blue'
    },
];

onMount(() => {
    // Créer le stage Konva
    stage = new Konva.Stage({
        container: 'konva-container',
        width: width,
        height: height,
    });

    // Créer une layer
    layer = new Konva.Layer();
    stage.add(layer);

    // Dessiner les marqueurs de temps
    for (let i = 0; i <= 20; i++) {
        const line = new Konva.Line({
            points: [i * 50, 0, i * 50, height],
            stroke: 'white',
            strokeWidth: 1,
        });

        const timeText = new Konva.Text({
            x: i * 50 + 5,
            y: height - 20,
            text: `${i}s`,
            fontSize: 12,
            fill: 'white',
        });

        layer.add(line);
        layer.add(timeText);
    }

    // Dessiner les clips
    clips.forEach((clip, index) => {
        const rect = new Konva.Rect({
            x: clip.start * 50,
            y: index * 60,
            width: clip.duration * 50,
            height: 50,
            fill: clip.color,
            cornerRadius: 10,
            stroke: 'white',
            strokeWidth: 1,
            draggable: true,

        });
        // Changer le curseur de la souris en pointeur lorsqu'on survole le clip
        rect.on('mouseover', () => {
            stage.container().style.cursor = 'pointer';
        });
        rect.on('mouseout', () => {
            stage.container().style.cursor = 'default';
        });
        // Ajouter le texte de durée
        const text = new Konva.Text({
            x: clip.start * 50 + 10,
            y: index * 60 + 15,
            text: `${clip.duration}s`,
            fontSize: 14,
            fill: 'white',
        });

        // Synchroniser le texte avec le rectangle pendant le drag
        rect.on('dragmove', (e) => {
            const newPosX = e.target.x();
            rect.y(index * 60); // Contraindre la position Y
            text.x(newPosX + 10); // Déplacer le texte avec le rectangle
            text.y(index * 60 + 15); // Garder la position Y du texte fixe
            layer.batchDraw(); // Redessiner la layer pour afficher le changement
        });

        // Ajuster la position finale lors du relâchement du drag
        rect.on('dragend', (e) => {
            const newStart = Math.max(0, Math.round(e.target.x() / 50));
            const newX = newStart * 50;
            rect.x(newX); // Ajuster le rectangle
            text.x(newX + 10); // Ajuster le texte
            layer.batchDraw();
        })

        layer.add(rect);
        layer.add(text);
    });

    // Dessiner la layer
    layer.draw();
});

onDestroy(() => {
    // Nettoyage
    if (stage) {
        stage.destroy();
    }
});
</script>

<style>
#konva-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #333333;
}
</style>

<div id="konva-container"></div>
