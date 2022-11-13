<script>
	import {goto} from "$app/navigation";
	import {supabase} from "../../supabase"
	import { currPlay } from '../../stores';
	let select;
	let selection;
	let clickedPlay
	export let img
	let click;
	let clicked = click ? true : false;	
	

	async function data (item) {
        console.log(item)
		const { data, error } = await supabase
        .from('playbook')
            .select()
            .eq("name", item);
            img = data[0];
			console.log(img)
			console.log(error)
			if (!img) {
				const { data, error } = await supabase
        		.from('publicplays')
            	.select()
				.eq("name", item);
				img = data[0];
				console.log(img)
				const { signedURL, errorTwo } = await supabase.storage
            .from('plays')
            .createSignedUrl("public/" + img.file_path, 60)
            if (signedURL == null) {
            img.signedURL = 'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA'
             } else {
            img.signedURL = signedURL;
             }
             return img;
            } else {
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
		}

	function gotoPlay(clickedPlay) {
		
		clickedPlay = data(clickedPlay)
		currPlay.set(clickedPlay);
		goto("/currplay");
	}

</script>

<div id = {img.formation.toUpperCase()} on:click|preventDefault={gotoPlay(img.name)} >
<div class="h-full aspect-w-1 aspect-h-1 w-full  hover:opacity-95  hover:cursor-pointer shadow-xl mx-auto flex rounded-3xl">
	<div class="card rounded-3xl">
		<figure>
			<img class="object-fill " loading="lazy" on:click={()=>{
				clicked = !clicked;
			}} src={img.signedURL} crossorigin="anonymous" alt="random img"/></figure>
		<div class="card-body bg-slate-700 rounded-b-2xl">
		  <h2 class="card-title text-white">
			{img.name}
		  </h2>

		  <div class="card-actions justify-end">
			<div class="badge badge-primary">{img.third_down}</div>
			<div class="badge bg-orange-500 text-white border-none">{img.concept}</div>
		  </div>
		</div>
	  </div>
  </div>
</div>
