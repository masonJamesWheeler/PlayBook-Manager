<script>
import {supabase} from "../../supabase"
import {goto} from "$app/navigation"
import Navbar from "../../components/navbar.svelte";
	import { dataset_dev, validate_component } from "svelte/internal";
	
let address;
let uuid = supabase.auth.user()?.id;

let sent = "hidden";
let email="";


function sendRequest() {
console.log(email)
sendInvite(email);
}

async function sendInvite (address) {
    console.log(address)
    const { data, error } = await supabase
  .from('profiles')
  .update({ linkcoach : uuid })
  .eq("email",address);
  console.log(data, error);
  if (error == null) {
    sent = "";
  }
}

async function removeAthlete (address) {
 console.log(address);
 const { data, error } = await supabase 
 .from('profiles')
 .update({linkcoach : null,
          joined: false
}) 
 .eq("email", address)
 console.log(data ,error)
 location.reload();

}

async function loadAthlete () {
   const { data, error } = await supabase
  .from('profiles')
  .select()
  .match({linkcoach : uuid, joined:true});
  

  console.log(data, error);
  return data;
}

async function loadPersonal () {
   const { data, error } = await supabase
  .from('profiles')
  .select()
  .eq("id",uuid);
  
  console.log(data , error)
  return data;
}

</script>
<Navbar/>
{#await loadAthlete() then value}
{#await loadPersonal() then personal}

<div class = " grid justify-center w-full mx-auto self-center my-4">
<div class="stats bg-primary text-primary-content">
  
    <div class="stat">
      <div class=" text-white"></div>
      <div class="stat-value">{personal[0].full_name}</div>
      <div class="stat-actions">
        <label for="my-modal-6" class="btn modal-button text-white">Edit Info</label>


<input type="checkbox" id="my-modal-6" class="modal-toggle" />
<div class="modal modal-bottom sm:modal-middle">
  <div class="modal-box">
    <h3 class="font-bold text-lg">Congratulations random Internet user!</h3>
    <p class="py-4">You've been selected for a chance </p>
    <div class="modal-action">
      <label for="my-modal-6" class="btn">Yay!</label>
    </div>
  </div>
</div>

      </div>
    </div>
    
    <div class="stat">
      <div class="stat-title">Total Athletes</div>
      <div class="stat-value">{value?.length}</div>
      <div class="stat-actions">
        <label for="my-modal-7" class="btn modal-button bg-green-500 border-none text-white hover:bg-green-600">Add Player</label>

<!-- Put this part before </body> tag -->
<input type="checkbox" id="my-modal-7" class="modal-toggle" />
<div class="modal modal-bottom sm:modal-middle">
  <div class="modal-box align-bottom">
    <ul class = "my-auto flex">
    <h3 class = "text-white font-bold text-2xl">Enter Player's Email</h3>
    <label for="my-modal-7" class="btn mx-auto mr-0">Escape</label>

</ul>
    

    <div class="modal-action ml-2">
        <input type="text" bind:value={email} placeholder="Type here" class="input input-bordered input-success w-full max-w-xs my-auto mb-2 ml-2" />
        <label for="my-modal-7" class="btn" on:click|preventDefault={sendRequest}>Submit</label>
    </div>
    <div class = "mx-auto  {sent} w-full col-span-3">
        <div class="alert alert-success shadow-lg mx-auto w-full col-span-3">
          <div>
            <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current flex-shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>
            <span>Invite has been sent!</span>
          </div>
          </div>
        </div>
  </div>
</div>


<!-- Put this part before </body> tag -->


      </div>
    </div>
    
  </div>
  </div>
<div class="overflow-x-auto">
    <table class="table table-compact table-zebra w-full self-center text-center">
      <thead>
        <tr>
          
          <th>Name</th> 
          <th>Email</th> 
          <th>Phone Number</th> 
          <th>
            <label>
            <input type="checkbox" class="checkbox hidden" />
          </label>
        </th>
        </tr>
      </thead> 
      <tbody>
       
       {#each value as val}

        <tr class = "text-white">
            <th class = "text-lg font-semibold">{val.full_name}</th> 
            <td class = "text-lg font-semibold">{val.email}</td> 
            <td class = "text-lg font-semibold">{val.phone}</td> 
            <th>
            <button on:click = { () => removeAthlete(val.email)} class=" btn bg-red-500 border-none text-white hover:bg-red-600" >Delete</button>
            </th>
          </tr>
          {/each}
          
      </tbody>
      </table>
      </div>
      {/await}

      {/await}