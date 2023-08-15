<script lang="ts">
    import type { Audio } from "../data/Song";
    import { audioSrc } from "../Store";
    import axios from "axios";
    export let song: Audio;
    let progress = "";
    let url = "";
    async function load() {
        progress = "generating url...";
        const start = performance.now();
        const res = await axios.get('https://onlyaudioapi.hypernova101.repl.co/audio', {
            headers: {
                'id' : song.id
            }
        });
        progress = `Done : ${(performance.now() - start).toFixed()} ms`;
        if(res.status == 200) {
            url = res.data.url;
        } else {
            progress = "Error occurred...";
        }
    };
    load();
    async function clicker() {
        audioSrc.set(url);
    };
</script>
<main>
    <div class="w-full" on:click={clicker}>  
        <div class="bg-slate-600 rounded-3xl shadow-2xl shadow-slate-900 flex flex-row m-4 hover:bg-slate-800">
          <img src={song.thumbnailUrl} alt="thumbnail" srcset="" class="w-[120px] h-[90px] rounded-xl m-2 shadow-2xl">
            <div class="flex flex-col my-3">
              <h4 class="text-white">{song.title}</h4>
              <p class="text-white">{progress}</p>
            </div>
        </div>
    </div>
</main>
