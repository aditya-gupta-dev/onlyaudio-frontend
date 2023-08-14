<script lang="ts">
  import type { Audio } from "./data/Song";
  import ErrorPage from "./lib/ErrorPage.svelte";
  import Song from "./lib/Song.svelte";
  import axios from 'axios';
  import { audioSrc } from "./Store";
  import AudioPlayer from "./lib/AudioPlayer.svelte";

  let errorOccured = false;
  let text: string = "";
  let perf: string = "";
  let audios: Array<Audio> = new Array<Audio>();

  async function buttonClick() {
    audios = [];
    audioSrc.set("");
    const query = text;
    text = "";
    try {
      const start = performance.now();
      perf = "Fetching...";
      
      const response = await axios.get<Array<Audio>>(
        "http://localhost:3000/video", {
          headers: {
            "q" : query
          }
        }
      );

      perf = `Time took : ${(performance.now() - start).toFixed()} ms`;
      
      if(response.status == 200) {
        audios = response.data;
      }
    } catch {
      errorOccured = true
    } 
  }
</script>

<main>

  {#if errorOccured}
      <ErrorPage/>
    {:else}
      {#each audios as audio}
        <Song song={audio}/>
      {/each}
      <div class="h-screen flex flex-col justify-between">
        <div class="fixed bottom-0 left-0 w-full bg-slate-900 text-black p-4 flex flex-col justify-between items-center">
          <p class="text-white">{perf}</p>
          <AudioPlayer url={$audioSrc}/>
          <div class="flex flex-row w-full">
            <input type="text" class="px-4 py-2 w-full rounded-lg mr-2" placeholder="Enter your search term" bind:value={text}>
            <button class="px-4 py-2 text-white font-semibold rounded-lg shadow-lg bg-slate-600 hover:bg-slate-950 transition-all" on:click={buttonClick}>Search</button>
          </div>
        </div>
      </div>
  {/if} 
</main>

