<script lang="ts">
    import Hls from "hls.js";
    import { onMount } from "svelte";

    let { video = $bindable(), ready = $bindable(), src = "", photos = $bindable([]) } = $props();

    onMount(() => {
        if (!video) {
            console.error("Video element is not bound.");
            return;
        }
        if (video?.canPlayType("application/vnd.apple.mpegurl") == "probably") {
            video.src = src;
        } else if (Hls.isSupported()) {
            const hls = new Hls({debug: true});
            hls.loadSource(src);
            hls.attachMedia(video);
            video.addEventListener("play", () => {
                ready = true;
            });
        } else {
            console.error("HLS is not supported in this browser.");
        }
    });

</script>

<style>
    .preview-container {
        width: 100%;
        aspect-ratio: 315 / 420;
        border: 3px solid var(--secondary-color);
        border-radius: 4px;
        box-shadow: 0px 0px 10px 5px rgba(from var(--accent-color) r g b / 0.25);
        overflow: hidden;
        padding: 0;
        background: repeat url("../image/pattern.png");
        animation: scrollBackground 30s linear infinite;
        position: relative;
    }

    .preview-video {
        position: absolute;
        left: 50%;
        transform: translateX(-50%) translateY(-18%);
        height: 400%;
        width: auto;
        opacity: 1;
        filter: blur(0px);
        transition: opacity 0.5s linear, filter 0.5s linear;
    }

    .preview-video.hidden {
        opacity: 0;
        filter: blur(10px);
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

<div class="preview-container">
    <video 
        class={["preview-video", ready ? "" : "hidden"]} 
        bind:this={video} 
        muted autoplay 
        playsinline={true}
        crossorigin="anonymous" bind:this={video}
    >  
        Sorry, something went wrong with the video stream. Try refreshing the page.
    </video>
</div>