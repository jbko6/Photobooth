<script lang="ts">
    import { fade, fly } from "svelte/transition";
    import Checkmark from "./Checkmark.svelte";
    import PhotoPreview from "./PhotoPreview.svelte";

    const PHOTO_COUNT = 3;

    let { 
        onComplete, 
        flash, 
        captures = $bindable([]) 
    } = $props();

    let snapReady = $state(false);
    let countdown = $state(-1);
    let photosTaken = $state(0);
    let video = $state<HTMLVideoElement | null>(null);
    let canvases = $state(new Array(PHOTO_COUNT).fill(new OffscreenCanvas(315, 420)));

</script>

<style>
    .photo-view-container {
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        padding-top: 10%;
        width: 85%;
    }

    .photos-controls-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        margin-top: 10%;
        height: 80px;
        width: 100%;
    }

    .photo-markers {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 1em;
    }

    .countdown {
        justify-self: flex-end;
        background: none;
        border: 4px solid var(--secondary-color);
        width: 100px;
        height: 100px;
        border-radius: 50%;
        position: relative;
    }

    .inner-countdown {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        width: 85px;
        height: 85px;
        border-radius: 50%;
        background-color: white;
        opacity: 0;
        transition: opacity 0.3s ease;
        font-size: 60px;
        font-weight: bolder;
    }

    .inner-countdown.active {
        background: radial-gradient(circle at center, var(--secondary-color) var(--color-position), rgba(from var(--secondary-color) r g b / 0) var(--transparent-position));
        opacity: 1;
    }

    .inner-countdown.counting {
        animation: 4s linear 3 forwards countdown;
    }

    .inner-countdown-text {
        color: white;
    }

    @property --color-position {
        syntax: '<percentage>';
        inherits: false;
        initial-value: 75%;
    }

    @property --transparent-position {
        syntax: '<percentage>';
        inherits: false;
        initial-value: 75%;
    }

    @keyframes countdown {
        25% {
            --color-position: 0%;
            --transparent-position: 0%;
        }
        26% {
            --color-position: 75%;
            --transparent-position: 75%;
        }
        50% {
            --color-position: 0%;
            --transparent-position: 0%;
        }
        51% {
            --color-position: 75%;
            --transparent-position: 75%;
        }
        75% {
            --color-position: 0%;
            --transparent-position: 0%;
        }
        100% {
            --color-position: 0%;
            --transparent-position: 0%;
        }
    }
    
</style>

<div out:fly={{x: 400}} in:fade={{delay:400}} class="photo-view-container">
    <PhotoPreview src="https://61e0c5d388c2e.streamlock.net/live/15_NE_Campus_NS.stream/playlist.m3u8" bind:ready={snapReady} bind:video />
    <div in:fade class="photos-controls-container">
        <div class="photo-markers">
            <Checkmark checked={photosTaken > 0} diameter=40 />
            <Checkmark checked={photosTaken > 1} diameter=40 />
            <Checkmark checked={photosTaken > 2} diameter=40 />
        </div>
        <button class="countdown" aria-label="countdown begin" onclick={() => {
            if (!snapReady) {
                return;
            }
            countdown = 3;
            const interval = setInterval(() => {
                if (countdown > 0) {
                    countdown--;
                    if (countdown === 0) {
                        // Flash the camera icon
                        flash();
                        canvases[photosTaken].getContext('2d')?.drawImage(video!!, -1055, -350, 2450, 1837);
                        captures.push(canvases[photosTaken].transferToImageBitmap());
                        photosTaken++;
                    }
                } else {
                    countdown = 3;
                    if (photosTaken >= 3) {
                        countdown = -1;
                        onComplete();
                        clearInterval(interval);
                    }
                }
            }, 1000);
        }}>
            <div transition:fade class="inner-countdown {snapReady ? 'active' : ''} {countdown >= 0 ? 'counting' : ''}">
                <div class="inner-countdown-text">{countdown > 0 ? countdown : ''}</div>
            </div>
        </button>
    </div>
</div>