<script>
import {supabase} from "../supabase"
import {onMount} from "svelte"
	import { dataset_dev } from "svelte/internal";
let uuid = supabase.auth.user()?.id;
let plays = new Map();
let show = false;
let playsArray;
let totalFormations= 0;
let totalPlays = 0;
let avatar_url = null;

onMount(async () => {
  const { data, error } = await supabase
  .from('playbook')
  .select('formation')
  .eq("user_id",uuid);
  data?.forEach(print);
  totalPlays = data?.length
  totalFormations = plays.size
  show = true;
  playsArray = Array.from(plays.keys());
  
  const { signedURL, errortwo } = await supabase.storage
    .from('plays')
    .createSignedUrl(uuid + "/avatar_url", 60)
     avatar_url = signedURL;
    console.log(signedURL, errortwo)
});
function print(item) {
  if(!plays.has(item.formation) && item.formation != "")
  
  plays.set(item.formation.toUpperCase() ,1)
}



</script>

<div class = "flex mt-4">
    <div class="stats shadow mx-auto bg-slate-800 w-full mx-6">
      
        <div class="stat">
          <div class="stat-figure text-white">
            <img class = "w-12 h-12" src = "https://cdn0.iconfinder.com/data/icons/clipboard-3/100/clipboard14-512.png"/>
          </div>
          <div class=" text-xl text-white opacity-100">Total Plays</div>
          <div class="stat-value text-primary">{totalPlays}</div>
          <div class="stat-desc">21% more than last month</div>
        </div>
        
        <div class="stat">
          <div class="stat-figure text-secondary">
            <img class = "w-12 h-12" src = 'https://cdn4.iconfinder.com/data/icons/football-41/24/ico-formation-512.png'/>
          </div>
          <div class="text-xl text-white opacity-100">Total Formations</div>
          <div class="stat-value text-secondary">{totalFormations}</div>
          <div class="stat-desc">21% more than last month</div>
        </div>
        
        <div class="stat">
          <div class="stat-figure text-secondary">
            <div class="avatar online">
              <div class="w-16 rounded-full">
                <img src= {avatar_url} />
              </div>
            </div>
          </div>
          <div class="stat-value text-white">86% Pass Plays</div>
          <div class="stat-title">Tasks done</div>
          <div class="stat-desc text-secondary">31 tasks remaining</div>
        </div>
        
      </div>
    </div>