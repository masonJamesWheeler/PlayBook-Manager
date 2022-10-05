
<script>
	import RandomImage from './playCard.svelte'
    import {supabase} from '../../supabase.js'
    import { dataset_dev, each } from "svelte/internal";

import {onMount} from "svelte"
import Navbar from "../../components/navbar.svelte";
import Stats from "../../components/stats.svelte"
import { prerendering } from "$app/environment";
let add = false;
let plays = new Map();
let show = false;
let playsArray = [];
let totalPlays;
let formations = new Map();
let third = new Map();
let formationArray = [];
let thirdArray = [];
let thisImage;
let currentURL = "";
let myFile;
let signedURL
let showDisplay = false;
let avatar_url;
let currentTab = ["tab-active", "", ""]
let coach = ""
let coachID = ""



	let userPlays = [];
    let bothPlays = [];
	let allImages = []
	let uuid = supabase.auth.user()?.id;
	let showing;
	$: showAllImages = showing ? true : false;
	
    const promise = data();
    const promiseTeam = getTeam();


    function changeTab(param) {
        currentTab = ["", "", ""]
        currentTab[param] = "tab-active"
    }

    async function saveWholePlay (item) {
    const { signedURL, error } = await supabase.storage
    .from('plays')
    .createSignedUrl(item.file_path, 60)
    if (signedURL == null) {
    item.signedURL = 'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA'
    } else {
    item.signedURL = signedURL;
    }
    formations.set(item.formation, 1);
    third.set(item.third_down,1);
    userPlays[userPlays.length] = item
    formationArray = Array.from(formations.keys());
    thirdArray = Array.from(third.keys())
    plays.set(item,1);
}

async function saveBothPlays (item) {
    const { signedURL, error } = await supabase.storage
    .from('plays')
    .createSignedUrl(item.file_path, 60)
    if (signedURL == null) {
    item.signedURL = 'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA'
    } else {
    item.signedURL = signedURL;
    }
    formations.set(item.formation, 1);
    third.set(item.third_down,1);
    bothPlays[bothPlays.length] = item
    formationArray = Array.from(formations.keys());
    thirdArray = Array.from(third.keys())
}
async function savePrivPlay (item) {
    const { signedURL, error } = await supabase.storage
    .from('plays')
    .createSignedUrl("public/" + item.file_path, 60)
    if (signedURL == null) {
    item.signedURL = 'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA'
    } else {
    item.signedURL = signedURL;
    }
    formations.set(item.formation, 1);
    third.set(item.third_down,1);
    userPlays[userPlays.length] = item
    formationArray = Array.from(formations.keys());
    thirdArray = Array.from(third.keys())
    plays.set(item,1);
}

    async function getTeam() {
        const{data,error} = await supabase
        .from('profiles')
        .select()
        .eq("id", uuid)
        console.log(error)
         coachID = data[0].linkcoach
          console.log(coachID)
            if (coachID == null) {
                console.log('doesnt belong to a team')
            } else {
            getTeamPlays(coachID);
            console.log(bothPlays)
            }
        
    }
    async function getTeamPlays(coachID) {
        const { data, error } = await supabase
        .from('playbook')
            .select()
            .eq("user_id",coachID);
            totalPlays = data;
            formationArray = [];
            thirdArray = []
            data?.forEach(saveBothPlays);
    }

	async function data() {
        const { data, error } = await supabase
        .from('playbook')
            .select()
            .eq("user_id",uuid);
            totalPlays = data;
            data?.forEach(saveWholePlay);
            privatedata();
            console.log(plays)
    }
    async function privatedata() {
        const { data, error } = await supabase
        .from('publicplays')
            .select()
            .eq("user_id",uuid);
            totalPlays = data;
            data?.forEach(savePrivPlay);
    }
    function removeCardFormation(param) {
    let editPlays = userPlays.filter(obj => obj.formation !== param.formation);
    let editBothPlays = bothPlays.filter(obj => obj.formation !== param.formation);
    
    if (userPlays.length == editPlays.length && bothPlays.length == editBothPlays.length) {
                addCardFormation(param)
                addCardCoachFormation(param)
     } else {
        if (userPlays.length == editPlays.length) {
        addCardFormation(param);
        console.log('success')
         } else {
        userPlays = editPlays;
        }  if (bothPlays.length == editBothPlays.length) {
        addCardCoachFormation(param);
        console.log("success")
    }  else {
        bothPlays = editBothPlays;
    }
}
}

    async function addCardFormation(param) {
        const { data, error } = await supabase
        .from('playbook')
            .select()
            .match({formation : param.formation, user_id:uuid});
            data?.forEach(saveWholePlay);
            addPrivCardFormation(param);
    }

    async function addCardCoachFormation(param) {
        const { data, error } = await supabase
        .from('playbook')
            .select()
            .match({formation : param.formation, user_id:coachID});
            data?.forEach(saveBothPlays);
            if (currentTab[1] == "tab-active") {
            addPrivCardFormation(param);
            }
    }

    async function addPrivCardFormation(param) {
        const { data, error } = await supabase
        .from('publicplays')
            .select()
            .match({formation : param.formation, user_id:uuid});
            data?.forEach(savePrivPlay);
    }

    function removeCardThird(param) {
        let editPlays = userPlays.filter(obj => obj.third_down !== param.currThird);
        let editBothPlays = bothPlays.filter(obj => obj.third_down !== param.currThird);
        // put in the case where we are at the both tab and then default to the lower code which works for the 
        //first two cases
            if (userPlays.length == editPlays.length && bothPlays.length == editBothPlays.length) {
                addCardThird(param)
                addCardCoachThird(param)
        
            } else {
        //
        if (userPlays.length == editPlays.length) {
        addCardThird(param);
        console.log('success')
         } else {
        userPlays = editPlays
        }
        if (bothPlays.length == editBothPlays.length) {
            addCardCoachThird(param);
            console.log("success")
        } else {
            bothPlays = editBothPlays
        }
    }
}
    async function addCardThird(param) {
        const { data, error } = await supabase
        .from('playbook')
            .select()
            .match({third_down : param.currThird, user_id:uuid});
            data?.forEach(saveWholePlay);
            addPrivCardThird(param);
    }

    async function addCardCoachThird(param) {
        const { data, error } = await supabase
        .from('playbook')
            .select()
            .match({third_down : param.currThird, user_id:coachID});
            data?.forEach(saveBothPlays);
            if (currentTab[1] == "tab-active") {
            addPrivCardThird(param);
            }
    }

    async function addPrivCardThird(param) {
        const { data, error } = await supabase
        .from('publicplays')
            .select()
            .match({third_down : param.currThird, user_id:uuid});
            data?.forEach(savePrivPlay);
    }


</script>
<Navbar/>
<Stats/>
<div class = "flex">
    <div class="drawer-side ">
        <label for="my-drawer-2" class="drawer-overlay"></label>
        <h6 class = "text-white font-bold text-2xl p-2">All Formations</h6>

        <ul class="menu overflow-y-auto px-1 w-48 bg-base-100 text-base-content max-h-48 ">
            <div>
                {#each formationArray as formation}
                <div class="form-control my-0">
                    <label class="label cursor-pointer">
                      <span class="font-semibold text-white text-lg">{formation.toUpperCase()}</span> 
                      <input type="checkbox" checked="checked" on:change|preventDefault={removeCardFormation({formation})} bind class="checkbox" />
                    </label>
                  </div>
            {/each}     
            
        </div>
        </ul>
        <div class = "flex mt-4">
            <div class="drawer-side ">
                <label for="my-drawer-2" class="drawer-overlay"></label>
                <h6 class = "text-white font-bold text-2xl p-2">Third-Down Preference</h6>

                <ul class="menu p-4 overflow-y-auto w-48 bg-base-100 text-base-content max-h-48 ">
                    <div>
                        {#each thirdArray as currThird}
                        <div class="form-control my-0">
                            <label class="label cursor-pointer">
                              <span class="font-semibold text-white text-lg">{currThird.toUpperCase()}</span> 
                              <input type="checkbox" checked="checked" on:change|preventDefault={removeCardThird({currThird})} bind class="checkbox" />
                            </label>
                          </div>
                    {/each}     
                    
                </div>
                </ul>
              </div>
              </div>
      </div>
      
      
      <div class=" w-full h-full">
        <div class="mx-auto max-w-5xl py-4 px-4 lg:max-w-7xl">
          <div class="tabs w-full align-center justify-center mx-auto">
            <a on:click={() => changeTab(0)} class="tab tab-lifted text-gray-300 font-bold {currentTab[0]} text-xl">Your Plays</a> 
            <a on:click={() => changeTab(1)} class="tab tab-lifted text-gray-300 font-bold {currentTab[1]} text-xl">Team's Plays</a> 
            <a on:click={() => changeTab(2)} class="tab tab-lifted text-gray-300 font-bold {currentTab[2]} text-xl">Both</a>
          </div>
          <div class="mt-6 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-y-10 gap-x-6 xl:gap-x-8">
        {#if currentTab[1] != "" || currentTab[2] == "tab-active"}
        {#await promiseTeam}
	    {:then _}
	    {#each bothPlays as img}
		<RandomImage {img}/>
	    {/each}
         {/await}
        {/if}
        {#if currentTab[1] != "tab-active" || currentTab[2] == "tab-active"}
        {#await promise}
	    {:then _}
	    {#each userPlays as img}
		<RandomImage {img}/>
	    {/each}
         {/await}
         {/if}
        </div>
    </div>
  </div>
</div>



