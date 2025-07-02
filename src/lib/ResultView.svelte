<script lang="ts">
    import { onMount } from "svelte";
    import { linear } from "svelte/easing";
    import { fade, fly } from "svelte/transition";

    let {
        captures = $bindable(),
        startOver
    }: {
        captures: ImageBitmap[];
        startOver: () => void;
    } = $props();

    let stripCanvas: HTMLCanvasElement | null = null;
    let complete = $state(false);

    function savePhotoStrip() {
        if (!stripCanvas) return;
        
        // Create a download link
        const link = document.createElement('a');
        
        // Convert canvas to blob and create download URL
        stripCanvas.toBlob((blob) => {
            if (!blob) return;
            
            const url = URL.createObjectURL(blob);
            link.href = url;
            
            // Generate filename with timestamp
            const timestamp = new Date().toISOString().slice(0, 19).replace(/:/g, '-');
            link.download = `photobooth-strip-${timestamp}.png`;
            
            // Trigger download
            document.body.appendChild(link);
            link.click();
            
            // Cleanup
            document.body.removeChild(link);
            URL.revokeObjectURL(url);
        }, 'image/png', 1.0); // High quality PNG
    }

    onMount(() => {
        const ctx = stripCanvas?.getContext("2d");
        if (!ctx) {
            console.error("Failed to get canvas context");
            return;
        }

        // Fill the entire canvas with a background color
        ctx.fillStyle = "#FFDCEB"; // Using CSS variable
        ctx.fillRect(0, 0, stripCanvas!!.width, stripCanvas!!.height);
        console.log("Canvas initialized and filled with background color");

        // Draw each photo onto the strip canvas
        captures.forEach((capture, index) => {
            ctx.drawImage(capture, 11, 10 + index * 430, 315, 420);
            console.log(`Photo ${index + 1} drawn on strip canvas`);
        });
        const header = new Image();
        header.src = "assets/image/header.png";
        header.onload = () => {
            ctx.drawImage(header, 50, 1300, 245, 85);
            console.log("Header drawn on strip canvas");
        }
        setTimeout(() => {
            console.log("Photo strip animation complete");
            complete = true;
        }, 6500); // Adjust the timeout as needed
    });


</script>

<style>
    .resultsview-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding-top: 3em;
        width: 85%;
    }

    .slot {
        width: 50vw;
        max-width: 400px;
        height: 5px;
        border-radius: 4px;
        border: 3px solid var(--secondary-color);
        box-shadow: 0px 0px 10px 5px rgba(from var(--accent-color) r g b / 0.25);
        position: relative;
    }

    .photo-strip-container {
        width: 100%;
        height: 68vh;
        position: absolute;
        overflow: hidden;
        transition: transform 1s ease-in-out;
    }

    .photo-strip-container.complete {
        transform: translateY(-8vh);
    }

    .photo-strip {
        height: 65vh;
        width: auto;
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        box-shadow: 0px 8px 8px 0px rgba(from var(--primary-color) r g b / 0.25);
    }

    .button-container {
        position: absolute;
        left: 50%;
        transform: translateX(-50%);
        bottom: 5%;
        display: flex;
        gap: 2em;
    }

    .start-over-button,
    .save-button {
        background: none;
        border: 3px solid var(--secondary-color);
        border-radius: 4px;
        padding: 10px;
        font: var(--sans-serif-font);
        font-size: 20px;
        text-wrap: nowrap;
    }

    .save-button {
        background-color: var(--secondary-color);
        color: white;
    }
</style>


<div in:fade={{delay:500}} class="resultsview-container">
    <div class="slot">
        <div class={["photo-strip-container", {complete}]}>
            <canvas in:fly={{y: -400, delay: 1000, duration: 5000, easing: linear}} bind:this={stripCanvas} width="335" height="1390" class="photo-strip"></canvas>
        </div>
    </div>
    {#if complete}
        <div in:fade={{delay: 200}} class="button-container">
            <button class="start-over-button" onclick={() => {
                startOver();
            }}>Start over</button>
            <button class="save-button" onclick={() => {
                savePhotoStrip();
            }}>Save</button>
        </div>
    {/if}
    
</div>