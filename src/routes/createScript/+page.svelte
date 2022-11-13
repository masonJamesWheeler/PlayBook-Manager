<script>
import {supabase} from "../../supabase"
import {onMount} from "svelte"
import {goto} from "$app/navigation";
import {tableData} from './stores'
import Table from './table.svelte'

let img;
let context = "";
let mapOfPlays = new Map();
let possibilities = []
let images = [];
let uuid = supabase.auth.user()?.id;
let searchText = ""
let playQuant = 0;
let play;

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
   

  async function textSearch () {
    const { data, error } = await supabase
  .from('playbook')
  .select('name')
  .ilike('name', "%" + searchText + "%")
   possibilities = data;
   playQuant = data?.length;
}

</script>


    
<div class = "bg-gradient-to-r from-slate-200 to-slate-100 w-full">
<div class = " grid grid-cols-3 my-6 ml-6">
    <input type="text" placeholder="Script Name Ex.(Oregon Tuesday)" class="input input-bordered w-full max-w-xs" />
    
      <select class="select select-primary w-full max-w-xs mx-6">
        <option disabled selected>Make a script from a Install?</option>
        <option>Game of Thrones</option>
        <option>Lost</option>
        <option>Breaking Bad</option>
        <option>Walking Dead</option>
      </select>
</div>
<div class="overflow-x-auto shadow-2xl rounded-b-2xl mx-6">
</div>


<Table/>








</div>