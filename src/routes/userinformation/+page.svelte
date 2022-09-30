<script>
	import { goto } from '$app/navigation';
	import { redirect } from '@sveltejs/kit';
    import { supabase } from '../../supabase'
    import { user } from './sessionStore'
    let full_name = "";
    let team_name = ""
    let loading = true
    let username = null
    let website = null
    let uuid = supabase.auth.user()?.id;
    let files;
    let avatar_url = uuid;
    let phone = "";
    let greeting = "Let's get your playbook dialed"
    let hideError = "hidden"
    let fileLoaded = "hidden";
    let email = supabase.auth.user()?.email
    console.log(email);
    function getProfile(node) {
    try {
      loading = true;
      const user = supabase.auth.user();
  
      supabase
        .from("profiles")
        .select(`username, website, avatar_url`)
        .eq("id", user?.id)
        .single()
        .then(({ data, error, status }) => {
          if (data) {
            username = data.username;
            website = data.website;
            avatar_url = data.avatar_url;
            
          }
          if (error && status !== 406) throw error;
        });
      } catch (error) {
        alert(error.message);
      } finally {
        loading = false;
      }
    }
  
    async function updateProfile() {
      try {
        loading = true
        const user = supabase.auth.user()
        const updates = {
          id: user.id,
          team_name,
          avatar_url,
          full_name,
          phone,
          email:email,
          updated_at: new Date(),
        }
  
        let { error } = await supabase.from('profiles').upsert(updates, {
          returning: 'minimal', // Don't return the value after inserting
        })
        let filePathUrl = uuid + "/avatar_url";
        const {dataTwo, errorTwo } = await supabase.storage
        .from('plays')
        .upload(filePathUrl, files[0], {
        })
        
        if (!errorTwo && !error) {
        goto("/plays");
      }
        
  
        if (error) throw error
      } catch (error) {
        alert(error.message)
      } finally {
        loading = false
      }
      
    }
  
    async function signOut() {
      try {
        loading = true
        let { error } = await supabase.auth.signOut()
        if (error) throw error
      } catch (error) {
        alert(error.message)
      } finally {
        loading = false
      }
  
    }
    function checksInputs() {
      if(team_name != "" && phone != "" && full_name != "") {
        hideError = "hidden"
        updateProfile();
        
      } else {
        hideError = ""
      }
    }
    function fileLoad () {
      fileLoaded = "";
    }
  </script>
  <div>
    <div class="bg-no-repeat bg-cover bg-center relative" ><div class="absolute opacity-75 inset-0 z-0"></div>
  <div class="min-h-screen sm:flex sm:flex-row mx-0 justify-center">
      <div class="flex-col flex  self-center p-10 sm:max-w-5xl xl:max-w-2xl  z-10">
        <div class="self-start hidden lg:flex flex-col  text-white">
          <img src="" class="mb-3">
          <h1 class="mb-3 font-bold text-6xl"> {greeting} </h1>
        </div>
      </div>
      <div class="flex justify-center self-center z-10 mx-10">
        <div class="p-12 bg-white mx-auto rounded-2xl w-100 ">
            <div class="mb-4">
              <h3 class="font-semibold text-2xl text-gray-800">Hello! </h3>
              <p class="text-gray-500">Please Enter Information to Complete Sign-Up.</p>
            </div>
            <div class="space-y-5">
                        <div class="space-y-2">
                              <label class="text-sm font-medium text-gray-800 tracking-wide">Full Name</label>
              <input class=" w-full text-base px-4 py-2 border  border-gray-300 rounded-lg focus:outline-none focus:border-primary text-black" type="name" bind:value={full_name} placeholder="John Smith">
              </div>
                          <div class="space-y-2">
              <label class="mb-5 text-sm font-medium text-gray-800 tracking-wide">
                Phone Number
              </label>
              <input class="w-full content-center text-base px-4 py-2 border  border-gray-300 rounded-lg focus:outline-none focus:border-primary text-black" type="phone" bind:value={phone} placeholder="no dashes">
              <div class="space-y-2">
                <label class="mb-5 text-sm font-medium text-gray-800 tracking-wide">
                  Team or School Name
                </label>
                <input class="w-full content-center text-base px-4 py-2 border  border-gray-300 rounded-lg focus:outline-none focus:border-primary text-black" type="name" bind:value={team_name} placeholder="University of Something or Wildcats">
              </div>
              <div>
                
                <label> 
                  <input type="file" bind:files on:change={fileLoad} class="text-sm text-grey-500
                  file:mr-5 file:py-2 file:px-6
                  file:rounded-full file:border-0
                  file:text-sm file:font-medium
                  file:bg-gray-500 file:text-white
                  hover:file:cursor-pointer hover:file:bg-primary
                  hover:file:text-white text-transparent  my-4
                " /> <h3 class = "text-black text-xl font-bold ml-2 {fileLoaded}"> File loaded</h3>
              </label>
              <div class="alert alert-error shadow-lg col-span-3 text-center {hideError}">
                <div>
                  <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current flex-shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z" /></svg>
                  <span>Please fill out all fields</span>
                </div>
              </div>
              </div>
          </div>
              
          
            <div>
              <a type="submit" class="w-full max-w-lg flex justify-center bg-primary  hover:scale-105 text-gray-100 p-3  rounded-xl tracking-wide font-semibold  shadow-lg cursor-pointer transition ease-in duration-500" on:click={checksInputs}
                
              >Complete Sign-Up</a>
            </div>
            </div>
            <div class="pt-5 text-center text-gray-400 text-xs">
              <span>
                Beta
                </span>
            </div>
        </div>
      </div>
  </div>
</div>
</div>