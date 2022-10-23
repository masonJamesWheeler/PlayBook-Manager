<script>
import {supabase} from "../supabase"
import {onMount} from "svelte"
import { currPlay } from '../stores';
import {goto} from "$app/navigation";
import { page } from '$app/stores';
let img;
let context = "";
let mapOfPlays = new Map();
let possibilities = []
let images = [];
let uuid = supabase.auth.user()?.id;
let searchText = ""
let playQuant = 0;
let play;

async function textSearch () {
    const { data, error } = await supabase
  .from('playbook')
  .select('name')
  .ilike('name', "%" + searchText + "%")
   possibilities = data;
   playQuant = data?.length;
}
onMount(() => {
  async function getPlays(){
    const { data, error } = await supabase
        .from('playbook')
            .select()
            .eq("user_id",uuid);
            data?.forEach(saveImages);

}
getPlays();
});
async function saveImages (play) {
        const { signedURL, error } = await supabase.storage
        .from('plays')
        .createSignedUrl(play.file_path, 60)
         if (signedURL == null) {
        play.signedURL = 'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA'
        } else {
        play.signedURL = signedURL;
    }
        mapOfPlays.set([play.name, play.signedURL], 1)
    }
function setVar(clickedPlay) {
    clickedPlay = data(clickedPlay);
    currPlay.set(clickedPlay);
    goto("/currplay")
    }

async function data (item) {
        const { data, error } = await supabase
        .from('playbook')
            .select()
            .eq("name", item);
            img = data[0];
            const { signedURL, errorTwo } = await supabase.storage
            .from('plays')
            .createSignedUrl(img.file_path, 60)
            if (signedURL == null) {
            img.signedURL = 'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA'
             } else {
            img.signedURL = signedURL;
             }
             return img;
            }
   

</script>

<div class="navbar bg-white">
    <div class="navbar-start ">
      <div class="dropdown">
        <!-- svelte-ignore a11y-label-has-associated-control -->
        <label tabindex="0" class="btn btn-ghost btn-circle">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h7" /></svg>
        </label>
        <ul tabindex="0" class="menu menu-compact dropdown-content mt-3 p-2 shadow bg-base-100 rounded-box w-52">
        <li><a href = "/">Home</a></li>
          <li><a href = "/plays">Plays</a></li>
          <li><a>About</a></li>
        </ul>
      </div>
    </div>
    <div class="navbar-center mx-4">
      <!-- svelte-ignore a11y-missing-attribute -->
      <a class="btn btn-ghost normal-case text-xl text-blue-600">ProtoType 1</a>
    </div>
    <div class="navbar-end mx-4">
        <div class="dropdown">
            <input type="text"  on:keydown={textSearch} bind:value={searchText} placeholder="Search" class="input input-bordered" />
            {#if playQuant > 0}
            <ul tabindex="0" class="menu menu-compact dropdown-content mt-4 p-2 shadow bg-base-100 rounded-box w-52">
                {#if playQuant >= 1}  
                <li><a on:click|preventDefault={setVar(possibilities[0].name)}>{possibilities[0].name}</a></li>
                {/if}
                {#if playQuant >= 2}  
                <li><a on:click|preventDefault={setVar(possibilities[1].name)}>{possibilities[1].name}</a></li>
                {/if}
                {#if playQuant >= 3}  
                <li><a on:click|preventDefault={setVar(possibilities[2].name)}>{possibilities[2].name}</a></li>
                {/if}
              </ul>
              {/if}
        </div>
      <a href = "/organization">
      <button class="btn btn-ghost btn-circle mx-2" href = "/organization">
        <div class="indicator">
       <svg xmlns="https://img.icons8.com/external-flaticons-lineal-color-flat-icons/344/external-formation-american-football-flaticons-lineal-color-flat-icons.png" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9" /></svg>
       <span class="badge badge-xs badge-primary indicator-item"></span>
        </div>
      </button>
    </a>
    </div>
  </div>