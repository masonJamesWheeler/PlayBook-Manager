<script>
import { supabase } from "..//supabase";

let uuid = supabase.auth.user()?.id;
let joined;
let invitedBy;
let invited;
let name = null;

async function loadData() {
    const { data, error } = await supabase
  .from('profiles')
  .select()
  .eq("id",uuid)
  console.log(data, error)
  if (data[0].linkcoach != null) {
    invited = true;
    invitedBy = data[0].linkcoach
    name = loadCoachName(invitedBy)
    console.log(name)
    joined = data[0].joined
}
  
}
async function loadCoachName(name) {
    const { data, error } = await supabase
  .from('profiles')
  .select()
  .eq("id",name);
  console.log(data[0].team_name)
   return data[0].team_name
   console.log(data, error)
}
async function acceptInvite () {
    const { data, error } = await supabase
  .from('profiles')
  .update({ joined : true })
  .eq("id", uuid);
  console.log(data, error);
  loadData()
}
async function declineInvite () {
    const { data, error } = await supabase
  .from('profiles')
  .update({ 'linkcoach' : null })
  .eq("id", uuid);
  console.log(data, error);
  loadData()
}

</script>
{#await loadData() then}
{#await name then value}
{#if name != null && joined != true}

<footer class="footer w-full">
    <div>
        <div class="alert shadow-lg w-screen">
            <div>
              <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="stroke-info flex-shrink-0 w-6 h-6"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
              <span class = "text-white text-xl font-semibold">You have an Invitation from {value} </span>
            </div>
            <div class="flex-none">
              <button on:click|preventDefault={declineInvite} class = "btn btn-sm btn-ghost">Deny</button>
              <button on:click|preventDefault={acceptInvite} class="btn btn-sm btn-primary">Accept</button>
            </div>
          </div>
    </div>
  </footer>
  {/if}
{/await}
  {/await}