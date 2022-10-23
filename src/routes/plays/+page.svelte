<script>
	import RandomImage from './playCard.svelte';
	import { supabase } from '../../supabase.js';

	import { onMount } from 'svelte';
	import Navbar from '../../components/navbar.svelte';
	import Stats from '../../components/stats.svelte';
	import { prerendering } from '$app/environment';
	import { user } from '../userinformation/sessionStore';
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
	let currentURL = '';
	let myFile;
	let signedURL;
	let showDisplay = false;
	let avatar_url;
	let currentTab = ['tab-active', '', ''];
	let coach = '';
	let coachID = '';
	let playerRemovedPlays = [];
	let teamRemovedPlays = [];
	let removedFormations = [];
	let removedDowns = [];
	let removedParams = {
	    removedFormations,
		removedDowns
	};
	let selection = [];

	let userPlays = [];
	let bothPlays = [];
	let allImages = [];
	let uuid = supabase.auth.user()?.id;
	let showing;
	$: showAllImages = showing ? true : false;

	const promise = data();
	const promiseTeam = getTeam();

	function changeTab(param) {
		currentTab = ['', '', ''];
		currentTab[param] = 'tab-active';
	}

	async function saveWholePlay(item) {
		const { signedURL, error } = await supabase.storage
			.from('plays')
			.createSignedUrl(item.file_path, 60);
		if (signedURL == null) {
			item.signedURL =
				'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA';
		} else {
			item.signedURL = signedURL;
		}
		formations.set(item.formation, 1);
		third.set(item.third_down, 1);
		userPlays[userPlays.length] = item;
		formationArray = Array.from(formations.keys());
		thirdArray = Array.from(third.keys());
		plays.set(item, 1);
	}

	async function saveBothPlays(item) {
		const { signedURL, error } = await supabase.storage
			.from('plays')
			.createSignedUrl(item.file_path, 60);
		if (signedURL == null) {
			item.signedURL =
				'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA';
		} else {
			item.signedURL = signedURL;
		}
		formations.set(item.formation, 1);
		third.set(item.third_down, 1);
		bothPlays[bothPlays.length] = item;
		formationArray = Array.from(formations.keys());
		thirdArray = Array.from(third.keys());
	}
	async function savePrivPlay(item) {
		const { signedURL, error } = await supabase.storage
			.from('plays')
			.createSignedUrl('public/' + item.file_path, 60);
		if (signedURL == null) {
			item.signedURL =
				'https://res.cloudinary.com/cloudinary-marketing/images/w_1540,h_1083/f_auto,q_auto/v1649725549/Web_Assets/blog/loading-645268_1280/loading-645268_1280-jpg?_i=AA';
		} else {
			item.signedURL = signedURL;
		}
		formations.set(item.formation, 1);
		third.set(item.third_down, 1);
		userPlays[userPlays.length] = item;
		formationArray = Array.from(formations.keys());
		thirdArray = Array.from(third.keys());
		plays.set(item, 1);
	}

	async function getTeam() {
		const { data, error } = await supabase.from('profiles').select().eq('id', uuid);
		coachID = data[0].linkcoach;
		if (coachID == null) {
		} else {
			getTeamPlays(coachID);
		}
	}
	async function getTeamPlays(coachID) {
		const { data, error } = await supabase.from('playbook').select().eq('user_id', coachID);
		totalPlays = data;
		formationArray = [];
		thirdArray = [];
		data?.forEach(saveBothPlays);
	}

	async function data() {
		const { data, error } = await supabase.from('playbook').select().eq('user_id', uuid);
		totalPlays = data;
		data?.forEach(saveWholePlay);
		privatedata();
	}
	async function privatedata() {
		const { data, error } = await supabase.from('publicplays').select().eq('user_id', uuid);
		totalPlays = data;
		data?.forEach(savePrivPlay);
	}
	function removeCardFormation(param) {
		console.log('removed downs = ' + removedParams.removedDowns);
		console.log('removed formations = ' + removedParams.removedFormations);
		console.log('removed Plays' + playerRemovedPlays);

		if (!removedParams.removedFormations.includes(param.formation)) {
			removedParams.removedFormations[removedParams.removedFormations.length] = param.formation;
			userPlays.forEach((play) => {
				removedParams.removedFormations.forEach((removed) => {
					if (play.formation === removed) {
						playerRemovedPlays.push(play);
					}
				});
			});
			updatePlays();
		} else {
			playerRemovedPlays.forEach((removedPlay) => {
				if (removedPlay.formation === param.formation) {
					if (removedParams.removedDowns.length > 0) {
                        if (!removedParams.removedDowns.includes(removedPlay.third_down) ) {
								userPlays[userPlays.length] = removedPlay;
								playerRemovedPlays = playerRemovedPlays.filter(
									(obj) => obj.name !== removedPlay.name
								);
								removedParams.removedFormations = removedParams.removedFormations.filter(
									(obj) => obj !== param.formation
								);
							}
						
					} else {
						userPlays[userPlays.length] = removedPlay;
						playerRemovedPlays = playerRemovedPlays.filter((obj) => obj.name !== removedPlay.name);
					}
				}
			});
            removedParams.removedFormations = removedParams.removedFormations.filter(
									(obj) => obj !== param.formation
								);
		}
		console.log('removed downs = ' + removedParams.removedDowns);
		console.log('removed formations = ' + removedParams.removedFormations);
		console.log('removed Plays' + playerRemovedPlays);
	}
	function updatePlays() {
		if (removedParams.removedDowns.length !== 0 && removedParams.removedFormations.length !== 0) {
			removedParams.removedFormations.forEach((removedFormation) => {
				removedParams.removedDowns.forEach((removedDown) => {
					userPlays = userPlays.filter((obj) => obj.formation !== removedFormation);
					userPlays = userPlays.filter((obj) => obj.third_down !== removedDown);
				});
			});
		} else if (removedParams.removedDowns.length !== 0) {
			removedParams.removedDowns.forEach((removedDown) => {
				userPlays = userPlays.filter((obj) => obj.third_down !== removedDown);
			});
		} else if (removedParams.removedFormations.length !== 0) {
			removedParams.removedFormations.forEach((removedFormation) => {
				userPlays = userPlays.filter((obj) => obj.formation !== removedFormation);
			});
		}
	}

	function removeCardThird(param) {
		console.log('removed downs = ' + removedParams.removedDowns);
		console.log('removed formations = ' + removedParams.removedFormations);
		console.log('removed Plays' + playerRemovedPlays);

		if (!removedParams.removedDowns.includes(param.currThird)) {
			removedParams.removedDowns[removedParams.removedDowns.length] = param.currThird;
			userPlays.forEach((play) => {
				removedParams.removedDowns.forEach((removed) => {
					if (play.third_down === removed) {
						playerRemovedPlays.push(play);
					}
				});
			});
			updatePlays();
		} else {
			playerRemovedPlays.forEach((removedPlay) => {
				if (removedPlay.third_down === param.currThird) {
					if (removedParams.removedFormations.length > 0) {
                        if (!removedParams.removedFormations.includes(removedPlay.formation)) {
								userPlays[userPlays.length] = removedPlay;
								playerRemovedPlays = playerRemovedPlays.filter(
									(obj) => obj.name !== removedPlay.name
								);
							}
					} else {
						userPlays[userPlays.length] = removedPlay;
						playerRemovedPlays = playerRemovedPlays.filter((obj) => obj.name !== removedPlay.name);
					}
				}
			});
            removedParams.removedDowns = removedParams.removedDowns.filter(
							(obj) => obj !== param.currThird
						);
		}
		console.log('removed downs = ' + removedParams.removedDowns);
		console.log('removed formations = ' + removedParams.removedFormations);
		console.log('removed Plays' + playerRemovedPlays);
	}
</script>

<!-- {@debug removedParams}
{@debug playerRemovedPlays}
{@debug userPlays} -->

<div class="bg-gradient-to-r from-slate-200 to-slate-100 min-h-screen">
	<Navbar />
	<Stats />
	<div class="flex ">
		<div class="drawer-side bg-base-100 h-full rounded-b-2xl">
			<label for="my-drawer-2" class="drawer-overlay bg-base-100" />
			<h6 class="text-slate-700 font-bold text-2xl p-2">All Formations</h6>

			<ul class="menu overflow-y-auto px-1 w-48 text-base-content max-h-48 ">
				<div>
					{#each formationArray as formation}
						<div class="form-control my-0">
							<label class="label cursor-pointer">
								<span class="font-semibold text-slate-800 text-lg">{formation.toUpperCase()}</span>
								<input
									type="checkbox"
									checked="checked"
									on:change|preventDefault={removeCardFormation({ formation })}
									class="checkbox"
								/>
							</label>
						</div>
					{/each}
				</div>
			</ul>
			<div class="flex mt-4">
				<div class="drawer-side ">
					<label for="my-drawer-2" class="drawer-overlay" />
					<h6 class="text-slate-800 font-bold text-2xl p-2">Third-Down Preference</h6>

					<ul class="menu p-4 overflow-y-auto w-48 text-base-content max-h-48 ">
						<div>
							{#each thirdArray as currThird}
								<div class="form-control my-0">
									<label class="label cursor-pointer">
										<span class="font-semibold text-slate-800 text-lg"
											>{currThird.toUpperCase()}</span
										>
										<input
											type="checkbox"
											checked="checked"
											on:change|preventDefault={removeCardThird({ currThird })}
											bind
											class="checkbox"
										/>
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
					<a
						on:click={() => changeTab(0)}
						class="tab tab-lifted text-slate-800 font-bold {currentTab[0]} text-xl">Your Plays</a
					>
					<a
						on:click={() => changeTab(1)}
						class="tab tab-lifted text-slate-800 font-bold {currentTab[1]} text-xl">Team's Plays</a
					>
					<a
						on:click={() => changeTab(2)}
						class="tab tab-lifted text-slate-800 font-bold {currentTab[2]} text-xl">Both</a
					>
				</div>
				<div
					class="mt-6 grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-y-10 gap-x-6 xl:gap-x-8"
				>
					{#if currentTab[1] != '' || currentTab[2] == 'tab-active'}
						{#await promiseTeam then _}
							{#each bothPlays as img}
								<RandomImage {img} />
							{/each}
						{/await}
					{/if}
					{#if currentTab[1] != 'tab-active' || currentTab[2] == 'tab-active'}
						{#await promise then _}
							{#each userPlays as img}
								<RandomImage {img} />
							{/each}
						{/await}
					{/if}
				</div>
			</div>
		</div>
	</div>
</div>
