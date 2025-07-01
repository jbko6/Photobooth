<script lang="ts">
    import { fade } from "svelte/transition";

    let props = $props();
    let clicked = $state(false);

</script>

<style>
    .prompt-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        padding: 10em 2em;
    }

    .prompt-text {
        font-size: 20px;
        margin-bottom: 27px;
        white-space: pre-wrap;
    }

    .prompt-button {
        border-radius: 4px;
        border: 3px solid var(--secondary-color);
        box-shadow: 0px 0px 10px 5px rgba(from var(--accent-color) r g b / 0.25);
        background: none;
        font-size: 20px;
        padding: 15px 45px;
    }

</style>

<div in:fade class={["prompt-container", props.class]} style={props.style}>
    <div out:fade class="prompt-text">{props.text}</div>
    <button class={["prompt-button", { clicked }]} onclick={() => {clicked = true; props.onClick()}}><div out:fade>{props.buttonText}</div></button>
</div>