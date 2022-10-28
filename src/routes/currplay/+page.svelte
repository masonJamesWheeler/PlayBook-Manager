<script>
import {currPlay} from "../../stores.js"
import {supabase} from "../../supabase"

import Navbar from "../../components/navbar.svelte";
let show = false;
let img;
let promise;

currPlay.subscribe(value => {
		promise = value;
	});
console.log(promise)

</script>
<div class = "bg-gradient-to-r from-slate-200 to-slate-100 min-h-screen">
<Navbar/>

{#await promise}
<h1> the play is loading</h1>
{:then value}
<div class = "h-full w-full">
<div class="card bg-base-100 shadow-2xl shad w-4/6 my-10 mb-10 mx-auto rounded-3xl">
  <figure><img class = "object-contain w-full " src={value.signedURL} alt="Album"/></figure>
  <div class="card-body bg-slate-700">
    <h2 class="card-title text-white font-extrabold text-2xl">{value.name}</h2>
    <div class="card-actions justify-end">
			<div class="badge badge-primary">{value.third_down}</div>
			<div class="badge bg-blue-700 text-white border-none">{value.concept}</div>
      <div class="badge bg-green-600 text-white border-none">{value.shift}</div>
      <div class="badge bg-orange-700 text-white border-none">{value.motion}</div>
      <div class="badge bg-white text-black border-none">{value.formation}</div>

		  </div>
  </div>
</div>
</div>
{/await}
</div>

