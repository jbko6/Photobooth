<script lang="ts">
    let { photoCanvas = $bindable() } = $props();
</script>

<style>
    .photo-container {
        height: 100%;
        aspect-ratio: 315 / 420;
        border-radius: 4px;
        border: 3px solid var(--secondary-color);
        box-shadow: 0px 0px 4px 5px var(--secondary-color) inset;
        padding: 0;
    }

    .photo {
        width: 100%;
        height: 100%;
        margin: 0;
        background: url("assets/image/pattern.png") repeat;
        animation: scrollBackground 30s linear infinite;
    }

    @keyframes scrollBackground {
        from {
            background-position: 0 0;
        }
        to {
            background-position: 480px 480px;
        }
    }
</style>

<div class="photo-container">
    <canvas class="photo" bind:this={photoCanvas}></canvas>
</div>