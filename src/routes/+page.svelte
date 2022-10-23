<script>
	// @ts-nocheck
	import Navbar from '../components/navbar.svelte';
	import { goto } from '$app/navigation';
	import { supabase } from '../supabase';
	import { currPlay } from '../stores';
	import Invitation from '../components/invitation.svelte';
	import { each } from 'svelte/internal';
	let uuid = supabase.auth.user()?.id;

	let click;
	let clicked = click ? true : false;
	let img;
	let arrayPlays = new Array();
	let value;

	async function getPlays() {
		const { data, error } = await supabase.from('publicplays').select();
		let arrayPlays = data;
		arrayPlays.forEach((element) => {
			element.signedURL = savePlay(element);
		});

		console.log(arrayPlays);
		console.log(arrayPlays[0].signedURL);
		return arrayPlays;
	}
	async function savePlay(item) {
		const { signedURL, errorTwo } = await supabase.storage
			.from('plays')
			.createSignedUrl('public/' + item.file_path, 60);
		if (signedURL == null) {
			item.signedURL =
				'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA';
		} else {
			item.signedURL = signedURL;
		}
		return item.signedURL;
	}
	async function sendInfo(item) {
		const { data, error } = await supabase.from('publicplays').select().eq('name', item);
		img = data[0];
		console.log(img);
		const { signedURL, errorTwo } = await supabase.storage
			.from('plays')
			.createSignedUrl('public/' + img.file_path, 60);
		if (signedURL == null) {
			img.signedURL =
				'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA';
		} else {
			img.signedURL = signedURL;
		}
		return img;
	}

	function gotoPlay(clickedPlay) {
		clickedPlay = sendInfo(clickedPlay);
		currPlay.set(clickedPlay);
		goto('/currplay');
	}

	async function getPlayParam(param, paramMatch) {
		if (param == null && paramMatch == null) {
			const { data, error } = await supabase.from('publicplays')
      .select();
			let arrayPlays = data;
			arrayPlays.forEach((element) => {
				element.signedURL = savePlay(element);
			});
			value = arrayPlays;
		} else {
			const { data, error } = await supabase
      .from('publicplays')
      .select()
      .eq(param, paramMatch);
			let arrayPlays = data;
			arrayPlays.forEach((element) => {
				element.signedURL = savePlay(element);
			});
			value = arrayPlays
		}
	}
</script>
<div class = "bg-gradient-to-r from-slate-200 to-slate-100">
<Navbar />
<div class="hero min-h-full my-2">
	<div
		class="z-0 flex object-center justify-center flex-row-reverse align-top rounded-2xl mx-4 "
	>
		{#await getPlays() then value}
			<div class="w-full mx-4 my-4 object-right ">
				<div
					class="h-full aspect-w-1 aspect-h-1 w-96 rounded-3xl  hover:opacity-90  hover:cursor-pointer shadow-xl mx-auto flex"
				>
					<div class="card shadow-3xl" on:click={() => gotoPlay(value[0].name)}>
						<figure>
							{#await value[0].signedURL then}
								<img class="object-fill" src={value[0].signedURL} alt="random img" />
							{/await}
						</figure>
						<div class="card-body bg-gradient-to-r from-slate-700 to-slate-700">
							<h2 class="card-title text-white text-center">
								{value[0].name}
							</h2> 

							<div class="card-actions justify-end">
								<div class="badge badge-primary">{value[0].third_down}</div>
								<div class="badge bg-orange-500 text-white border-none">{value[0].concept}</div>
								<div class="badge bg-green-600 text-white border-none hidden md:block">
									{value[0].shift}
								</div>
								<div class="badge bg-orange-700 text-white border-none hidden md:block">
									{value[0].motion}
								</div>
								<div class="badge bg-white text-black border-none hidden md:block">
									{value[0].formation}
								</div>
							</div>
						</div>
					</div>
				</div>
			</div>
		{/await}
		<div class="hidden sm:block">
			<h6 class=" text-slate-700  text-2xl md:text-5xl font-extrabold mx-4 mt-12 align-right">
				The Best Way To Store Your PlayBook
			</h6>
			<ul class="mt-8 font-bold">
				<h3 class="text-slate-700 mx-4 align-middle">
					- Sort plays by Formation, Concept, Third-Down-Preference, and More
				</h3>
				<h3 class="text-slate-700 mx-4 align-middle my-4">
					- Study tools for players based on player's needs
				</h3>
				<h3 class="text-slate-700 mx-4 align-middle my-4">
					- Create and Send installs to players automatically via email or web
				</h3>
				<h3 class="text-slate-700 mx-4 align-middle my-4 hidden md:block">
					- Analytic tools to balance your playbook
				</h3>
				<h3 class="text-slate-700 mx-4 align-middle my-4 hidden md:block">
					- Search Public Plays for Ideas and Inspiration
				</h3>
			</ul>
		</div>
	</div>
</div>

<div class="grid card rounded-box place-items-center mt-2 mx-16 shadow-2xl bg-gradient-to-r from-slate-100 to-slate-50 ">
	<div class="card  card-side shadow-xl w-full flex h-full">
		<div class="btn-group btn-group-vertical  ">
			<button class="btn text-slate-800 bg-gray-100 border-none rounded-r-none hover:bg-sky-200" on:click={() => getPlayParam('run_or_pass', 'RPO')}>RPO's</button>
			<button class="btn text-slate-800 bg-gray-100 border-none hover:bg-sky-200" on:click={() =>getPlayParam('run_or_pass', 'Pass')}>Pass</button>
			<button class="btn text-slate-800 bg-gray-100 border-none hover:bg-sky-200" on:click={() => getPlayParam('run_or_pass', 'Run')}>Run</button>
			<button class="btn text-slate-800 bg-gray-100 border-none hover:bg-sky-200" on:click={() => getPlayParam('concept', 'Mesh')}>Mesh</button>
			<button class="btn text-slate-800 bg-gray-100 border-none hover:bg-sky-200" on:click={() => getPlayParam('third_down', 'Third and Long')}>Third and Long</button>
      <button class="btn text-slate-800 bg-gray-100 border-none hover:bg-sky-200" on:click={() => getPlayParam('concept', 'Mesh')}>2x2</button>
			<button class="btn text-slate-800 bg-gray-100 border-none hover:bg-sky-200" on:click={() => getPlayParam('concept', 'Mesh')}>3x1</button>
			<button class="btn text-slate-800 bg-gray-100 border-none rounded-r-none hover:bg-sky-200"  on:click={() => getPlayParam('concept', 'Mesh')}>Quads</button>

    </div>
			<div class=" inline-flex  gap-x-6 xl:gap-x-4 mt-2 overflow-x-auto ">
				{#await getPlayParam() then}
        {#key value}
         {#each value as curr}
					<div class="w-64 mx-4 my-4 object-right ">
						<div
							class="h-full aspect-w-1 aspect-h-1 w-64 rounded-3xl  hover:opacity-90  hover:cursor-pointer shadow-xl mx-auto flex"
						>
							<div class="card shadow-2xl" on:click={() => gotoPlay(curr.name)}>
								<figure>
									{#await curr.signedURL then}
										<img class="object-fill" src={curr.signedURL} alt="random img" />
									{/await}
								</figure>
								<div class="card-body bg-gradient-to-r from-slate-700 to-slate-700">
									<h2 class="card-title text-white text-center">
										{curr.name}
									</h2>

									
										</div>
									</div>
								</div>
							</div>
              {/each}
              {/key}
				{/await}
			
		</div>
	</div>
</div>

<h1 class="text-3xl font-bold underline">test</h1>
<button class="btn btn-primary">Get Started</button>
<a class="btn btn-primary" href="/post">Get Started</a>
<a class="btn btn-primary" href="/login">sign in</a>
<a class="btn btn-primary" href="/signup">sign up</a>
<a class="btn btn-primary" href="/userinformation">user information</a>
<Invitation />
</div>