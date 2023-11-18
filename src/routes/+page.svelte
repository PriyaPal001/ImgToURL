<script>
	import supabase from '$lib';
	import { copy } from 'svelte-copy';
	import upload from '$lib/upload.svg';
	let userimg;
	let avatar;
	let fileinput;

	const onFileSelected = (e) => {
		let image = e.target.files[0];
		userimg = image;
		let reader = new FileReader();
		reader.readAsDataURL(image);
		reader.onload = (e) => {
			avatar = e.target.result;
		};
	};

	let imgname = crypto.randomUUID() + '.jpg';
	$: retriveimg = '';

	let getimg = async () => {
		try {
			let { data, error } = await supabase.storage.from('Images').getPublicUrl(imgname);
			// console.log(data);
			retriveimg = data?.publicUrl;
		} catch (e) {
			console.log(e);
		}
		imgname = crypto.randomUUID() + '.jpg';
		avatar = null;
	};
	// $: console.log(imgname);
	let uploadImage = async () => {
		try {
			let { data, error } = await supabase.storage.from('Images').upload(imgname, userimg);
			console.log(userimg);
			getimg();
		} catch (e) {
			console.log(e);
		}
	};


	let btntext="COPY URL"
</script>

<div id="app" class="h-screen flex text-center my-10">
	<div class=" border-gray-900 border rounded-lg m-8 md:p-3">
		<div class=" border-gray-900 border rounded-lg m-8 md:p-3">
			{#if avatar}
				<img class="avatar w-56 h-56" src={avatar} alt="d" />
			{:else}
				<img
					class="avatar hover:cursor-pointer w-56 h-56"
					src={upload}
					alt=""
					on:click={() => {
						fileinput.click();
					}}
				/>
			{/if}
			<input
				style="display:none"
				type="file"
				accept=".jpg, .jpeg, .png"
				on:change={(e) => onFileSelected(e)}
				bind:this={fileinput}
			/>
		</div>
		<button
			type="button"
			on:click={uploadImage}
			class="rounded-md bg-gray-900 px-3.5 py-2 text-sm font-semibold text-white shadow-sm hover:bg-gray-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-gray-900 mb-4"
			>Upload</button
		>
	</div>
	<div class="flex items-center border border-gray-700 m-8 rounded-md p-4 flex-wrap justify-center">
		{#if retriveimg.length > 0}
			<img class="avatar border border-gray-700 rounded-md" src={retriveimg} alt="" />
		{:else}
			<img class="avatar border border-gray-700 rounded-md" src={upload} alt="" />
		{/if}
		<button
			use:copy={retriveimg}
			on:click={() => {
				btntext="COPIED"
				setTimeout(() => {
					btntext="COPY URL"
				}, 1000);
			}}
			class="rounded-md bg-gray-900 px-3.5 py-2 text-sm font-semibold text-white shadow-sm hover:bg-gray-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-gray-900 m-3 h-fit"
		>
			{btntext}
			

		</button>
	</div>
</div>

<style>
	#app {
		display: flex;
		align-items: center;
		justify-content: center;
		flex-flow: column;
	}
	.avatar {
		display: flex;
		height: 200px;
		width: 200px;
	}
</style>
