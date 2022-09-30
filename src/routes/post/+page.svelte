<script>
	import { supabase } from '../../supabase.js';
	import { isOverlayOpen } from './overlay-store';
	import Overlay from './Modal.svelte';
	import Navbar from '../../components/navbar.svelte';
	import { user } from '../userinformation/sessionStore.js';
	import { error } from '@sveltejs/kit';
	let name = '';
	let motion = '';
	let run_or_pass = '';
	let run = 'Run';
	let pass = 'Pass';
	let rpo = 'RPO';
	let long = 'Third and Long';
	let medium = 'Third and Medium';
	let short = 'Third and Short';
	let inches = 'Third and Inches';
	let extralong = 'Third and Extra-Long';
	let twobytwo = '2x2';
	let threebyone = '3x1';
	let quads = 'Quads';
	let ten = '10';
	let eleven = '11';
	let twelve = '12';
	let thirteen = '13';
	let twentone = '21';
	let twentytwo = '22';
	let twentythree = '23';
	let twenty = '20';
	let personel = '';
	let formation = '';
	let shift = '';
	let concept = '';
	let numXnum = '';
	let selected = '';
	let answer = '';
	let pub;

	let uuid = supabase.auth.user()?.id;
	let container;
	let files;
	/**
	 * @type {HTMLImageElement}
	 */
	let image;
	let placeholder;
	let input;
	let showImage = false;
	let hideInput = '';
	let hideError = 'hidden';
	let hideSuccess = 'hidden';

	async function createPlay() {
		if ((pub = false)) {
			const { data, error } = await supabase
				.from('playbook')
				.insert([
					{
						name: name,
						formation: formation,
						motion: motion,
						shift: shift,
						third_down: selected,
						concept: concept,
						user_id: uuid,
						file_path: uuid + '/' + name.replace(/\s/g, ''),
						run_or_pass: run_or_pass,
						skill_alignment: numXnum,
						personel: personel
					}
				]);
			console.log('test');
			uploadImage();
			isOverlayOpen.set(false);
			hideSuccess = '';
		} else {
			const { data, error } = await supabase
				.from('publicplays')
				.insert([
					{
						name: name,
						formation: formation,
						motion: motion,
						shift: shift,
						third_down: selected,
						concept: concept,
						user_id: uuid,
						file_path: uuid + '/' + name.replace(/\s/g, ''),
						run_or_pass: run_or_pass,
						id: uuid,
						skill_alignment: numXnum,
						personel: personel
					}
				]);
			console.log('test');
			uploadImagePublic();
			isOverlayOpen.set(false);
			hideSuccess = '';
			console.log(data, error);
		}
	}

	async function uploadImage() {
		const uploadFile = files[0];
		let filePath = uuid + '/' + name.replace(/\s/g, '');
		const { data, error } = await supabase.storage.from('plays').upload(filePath, uploadFile, {});
		console.log(error);
	}

	async function uploadImagePublic() {
		const uploadFile = files[0];
		let filePath = 'public/' + uuid + '/' + name.replace(/\s/g, '');
		const { data, error } = await supabase.storage.from('plays').upload(filePath, uploadFile, {});
		console.log(error);
	}

	function onChange() {
		const file = input.files[0];
		if (file) {
			showImage = true;
			hideInput = 'hidden';
			const reader = new FileReader();
			reader.addEventListener('load', function () {
				image.setAttribute('src', reader.result);
			});
			reader.readAsDataURL(file);

			return;
		}
		showImage = false;
	}
	function showOverlay() {
		if (
			name != '' &&
			formation != '' &&
			motion != '' &&
			concept != '' &&
			shift != '' &&
			files != null
		) {
			isOverlayOpen.set(true);
			hideError = 'hidden';
		} else {
			hideError = '';
		}
	}
</script>

<div class="h-full">
	<Navbar />
	<div class="mx-auto flex  h-96 items-center justify-center mt-16">
		<input
			type="file"
			bind:files
			class="mx-auto {hideInput}"
			bind:this={input}
			on:change={onChange}
		/>

		<div bind:this={container}>
			{#if showImage}
				<img bind:this={image} src="" alt="Preview" class="object-cover h-96 rounded-lg" />
			{:else}
				<span bind:this={placeholder} />
			{/if}
		</div>
	</div>
	<div class="bg-base-300 h-96">
		<form class="form-widget h-full py-4 mt-6">
			<div class="grid grid-cols-3 gap-4 mx-auto my-8 px-4">
				<input
					type="text"
					bind:value={name}
					placeholder="Play Name"
					class="input input-bordered w-full "
				/>
				<input
					type="text"
					bind:value={formation}
					placeholder="Formation - No Direction"
					class="input input-bordered w-full "
				/>
				<input
					type="text"
					bind:value={motion}
					placeholder="Motion"
					class="input input-bordered w-full"
				/>
				<input
					type="text"
					bind:value={shift}
					placeholder="Shift"
					class="input input-bordered w-full "
				/>
				<input
					type="text"
					bind:value={concept}
					placeholder="Concept"
					class="input input-bordered w-full"
				/>

				<select
					class="select select-success w-full"
					bind:value={selected}
					on:change|preventDefault={() => (answer = '')}
				>
					<option value="" selected data-default disabled>Third-Down Preference</option>
					<option>{inches}</option>
					<option>{short}</option>
					<option>{medium}</option>
					<option>{long}</option>
					<option>{extralong}</option>
				</select>
				<select
					class="select select-primary w-full col-span-1 text-l font-bold text-white"
					bind:value={personel}
					on:change|preventDefault={() => (answer = '')}
				>
					<option value="" selected data-default disabled>Personel</option>
					<option>{ten}</option>
					<option>{eleven}</option>
					<option>{twelve}</option>
					<option>{thirteen}</option>
					<option>{twenty}</option>
					<option>{twentone}</option>
					<option>{twentytwo}</option>
					<option>{twentythree}</option>
				</select>
				<select
					class="select select-primary w-full col-span-1 text-l font-bold text-white"
					bind:value={numXnum}
					on:change|preventDefault={() => (answer = '')}
				>
					<option value="" selected data-default disabled>Skill Alignment</option>
					<option>{twobytwo}</option>
					<option>{threebyone}</option>
					<option>{quads}</option>
				</select>
				<select
					class="select select-primary w-full col-span-1 text-l font-bold text-white"
					bind:value={run_or_pass}
					on:change|preventDefault={() => (answer = '')}
				>
					<option value="" selected data-default disabled>Run or Pass</option>
					<option>{run}</option>
					<option>{pass}</option>
					<option>{rpo}</option>
				</select>
				<div class="form-control">
					<label class="label cursor-pointer">
						<span class="font-bold text-white text">Make Play Public?</span>
						<input type="checkbox" class="toggle toggle-primary" bind:checked={pub} on />
					</label>
				</div>
				<div class="alert alert-error shadow-lg col-span-3 text-center {hideError}">
					<div>
						<svg
							xmlns="http://www.w3.org/2000/svg"
							class="stroke-current flex-shrink-0 h-6 w-6"
							fill="none"
							viewBox="0 0 24 24"
							><path
								stroke-linecap="round"
								stroke-linejoin="round"
								stroke-width="2"
								d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z"
							/></svg
						>
						<span>Please fill out all fields</span>
					</div>
				</div>
				<button class="btn btn-outline btn-success " on:click|preventDefault={showOverlay}
					>Submit Play</button
				>

				<div class="mx-auto  {hideSuccess} w-full col-span-3">
					<div class="alert alert-success shadow-lg mx-auto w-full col-span-3">
						<div>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								class="stroke-current flex-shrink-0 h-6 w-6"
								fill="none"
								viewBox="0 0 24 24"
								><path
									stroke-linecap="round"
									stroke-linejoin="round"
									stroke-width="2"
									d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
								/></svg
							>
							<span>Your Play Has Been Added!</span>
						</div>
						<a href="/plays">
							<div class="flex-none">
								<button class="btn btn-sm">View Play</button>
							</div>
							<a />
						</a>
					</div>
				</div>
			</div>
			<div />
		</form>
	</div>
	{#if $isOverlayOpen}
		<Overlay>
			<div class="grid-rows-1 grid-flow-col gap-4 w-48 h-32 text-center">
				<h2 class="my-6 mx-auto">All Done?</h2>
				<button on:click|preventDefault={createPlay} class="btn btn-outline btn-success mx-auto "
					>Add Play</button
				>
			</div>
		</Overlay>
	{/if}
</div>
