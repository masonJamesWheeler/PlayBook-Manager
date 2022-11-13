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

{#await promise}
<h1> the play is loading</h1>
{:then value}
<div class="flex flex-col w-full lg:flex-row">
  <div class = "w-full my-auto">
    <div class="card bg-base-100 shadow-2xl shad w-4/6 my-10 mb-10 ml-12 rounded-3xl">
      <figure><img class = "object-contain w-full " src={value.signedURL} alt="Album"/></figure>
      <div class="card-body bg-slate-700 rounded-b-3xl">
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
  


  <div class="grid flex-grow card rounded-box place-items-start mr-12 my-10 min-h-96 bg-white shadow-2xl shad w-4/6">
    <div class="form-control w-full">
      <label class="label mx-auto">
        <span class="label-text">QB Notes</span>
      </label> 
      <textarea class="textarea textarea-bordered h-24 mx-10" placeholder="Places notes here"></textarea>
  </div>

  <div class="form-control w-full">
    <label class="label mx-auto">
      <span class="label-text">O-line Notes</span>
    </label> 
    <textarea class="textarea textarea-bordered h-24 mx-10" placeholder="Places notes here"></textarea>
</div>

<div class="form-control w-full">
  <label class="label mx-auto">
    <span class="label-text">Wide-Reciever Notes</span>
  </label> 
  <textarea class="textarea textarea-bordered h-24 mx-10" placeholder="Places notes here"></textarea>
</div>

<div class="form-control w-full">
  <label class="label mx-auto">
    <span class="label-text">Runningback Notes</span>
  </label> 
  <textarea class="textarea textarea-bordered h-24 mx-10" placeholder="Places notes here"></textarea>
</div>

<div class="form-control w-full mb-4">
  <label class="label mx-auto">
    <span class="label-text">Tightend Notes</span>
  </label> 
  <textarea class="textarea textarea-bordered h-24 mx-10" placeholder="Places notes here"></textarea>
</div>
<button class="btn my-4 mx-auto">Update </button>

</div>



</div>

{/await}
</div>

