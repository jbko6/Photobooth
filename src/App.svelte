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
    overflow: hidden;
  }

  .header-container {
    display: flex;
    height: 80%;
    transition: height 1s ease;
    padding-top: 15%;
    padding-bottom: 0;
  }

  .headerMoveUp {
    height: 110px;
  }
</style>

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