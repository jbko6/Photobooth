<script lang="ts">
    import { mount, onMount } from "svelte";
    import Header from "./lib/Header.svelte";
    import Prompt from "./lib/Prompt.svelte";
    import Photoview from "./lib/Photoview.svelte";
    import ResultView from "./lib/ResultView.svelte";

    enum AppState {
      Intro,
      IntroToPrompt,
      Prompt,
      Snap,
      Result
    }

    let main: HTMLElement | null = null;
    let header: Header | null = null;
    let headerMoveUp = $state(false);
    let appState = $state(AppState.Intro);
    let captures = $state([]);

    onMount(() => {
      // flash camera
      setTimeout(() => {
        if (header && appState === AppState.Intro) {
          header.flash();
          // move header up after 2 seconds
          setTimeout(() => {
            appState = AppState.IntroToPrompt;
            setTimeout(() => {
              appState = AppState.Prompt;
            }, 1000);
          }, 1000);
        }
      }, 800);
      
    })

</script>

<style>

  main {
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .desktop-warning {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100%;
    width: 100%;
  }

  .header-container {
    display: flex;
    height: 80%;
    transition: height 1s ease;
    padding-top: 2em;
    padding-bottom: 0;
  }

  .headerMoveUp {
    height: 110px;
  }
</style>

{#if document.documentElement.clientWidth < 800}
<main bind:this={main}>
  <div class={[ "header-container", appState != AppState.Intro ? "headerMoveUp" : "" ]}>
    <Header bind:this={header} />
  </div>
  {#if appState === AppState.Prompt}
    <Prompt
      text="     Ready to take your pics?
Stand in front of the QR code!"
      buttonText="I'm ready!"
      onClick={() => {
        appState = AppState.Snap;
      }}
    />
  {:else if appState === AppState.Snap}
      <Photoview onComplete={() => appState = AppState.Result} flash={header?.flash} bind:captures></Photoview>
  {:else if appState === AppState.Result}
      <ResultView bind:captures startOver={() => {appState = AppState.Snap; captures = []}}></ResultView>
  {/if}
</main>
{:else}
<main bind:this={main}>
  <div class="desktop-warning">
    <h1>Desktop Mode Not Supported</h1>
    <p>This photobooth is designed for mobile devices. Please use a mobile device to take photos.</p>
    <p>Thank you!</p>
  </div>
</main>
{/if}