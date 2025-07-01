<script lang="ts">
    let iconSrc = $state("assets/image/📷.png");

    export async function flash() {
        iconSrc = "assets/image/📸.png"
        setTimeout(() => {
            iconSrc = "assets/image/📷.png"
        }, 100);
    }
</script>

<style>
    .header-container {
        display: flex;
        flex-direction: column;
        justify-content: center;
    }

    .subtitle {
        font-size: 24px;
        letter-spacing: 2.4px;
    }

    .title-container {
        display: flex;
        align-items: center;
        transform: translateY(-12px);
    }

    .title {
        color: var(--secondary-color);
        font-family: var(--playful-font);
        font-size: 48px;
    }

    .icon {
        width: 48px;
        transform: rotate(10deg);
        margin-left: 23px;
    }
</style>

<div class="header-container">
    <div class="subtitle">15th Avenue</div>
    <div class="title-container">
        <div class="title">Photobooth</div>
        <img class="icon" src={iconSrc} alt="camera icon" />
    </div>
</div>