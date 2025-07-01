<script lang="ts">
    let { checked, diameter } = $props();
</script>

<style>
    .checkmark {
        border-radius: 50%;
        border: 4px solid var(--secondary-color);
        background-color: white;
        transition: background-color 0.3s ease;
    }

    .checked {
        background-color: var(--secondary-color);
    }
</style>

<div class={["checkmark", { checked }]} style={`width: ${diameter}px; height: ${diameter}px;`}></div>