
<script>
	import supabase from '$lib';
  import {copy} from 'svelte-copy'
  let userimg;
	let avatar; 
  let fileinput;
  $: console.log(avatar);
  const onFileSelected = (e) => {
    let image = e.target.files[0];
    userimg=image;
    let reader = new FileReader();
    reader.readAsDataURL(image);
    reader.onload = (e) => {
      avatar = e.target.result;
    };
  };

  let imgname=crypto.randomUUID() + ".jpg";
  $: retriveimg = "";

  let getimg = async () => {
    try {
				let { data, error } = await supabase.storage.from('Images').getPublicUrl(imgname);
        console.log(data);
        retriveimg=data?.publicUrl;
				return error ? null : data?.publicUrl;
			} catch (e) {
				console.log(e);
			}
  };

	let uploadImage = async () => {
    try {
      let { data, error } = await supabase.storage.from("Images").upload(imgname, userimg);
      console.log(userimg);
      getimg();
    } catch (e) {
      console.log(e);
    }
	};



</script>



<div id="app" class="">
  <h1>Upload Image</h1>

  {#if avatar}
    <img class="avatar" src={avatar} alt="d" />
  {:else}
    <img
      class="avatar"
      src="https://cdn4.iconfinder.com/data/icons/small-n-flat/24/user-alt-512.png"
      alt=""
    />
  {/if}
  <img
    class="upload"
    src="https://static.thenounproject.com/png/625182-200.png"
    alt=""
    on:click={() => {
      fileinput.click();
    }}
  />
  <div
    class="chan"
    on:click={() => {
      fileinput.click();
    }}
  >
    Choose Image
  </div>
  <input
    style="display:none"
    type="file"
    accept=".jpg, .jpeg, .png"
    on:change={(e) => onFileSelected(e)}
    bind:this={fileinput}
  />
</div>


<button on:click={uploadImage}> Upload </button>

<div>
  <div>
    {retriveimg}
  </div>
  <button use:copy={retriveimg}>
    COPY URL
  </button>
</div>

<style>
  div {
		width: 300px;
		min-height: 100px;
		border: 2px solid #ddd;
		margin-top: 15px;
		display: flex;
		align-items: center;
		justify-content: center;
		font-weight: bold;
		color: #ccc;
	}
  #app {
    display: flex;
    align-items: center;
    justify-content: center;
    flex-flow: column;
  }

  .upload {
    display: flex;
    height: 50px;
    width: 50px;
    cursor: pointer;
  }
  .avatar {
    display: flex;
    height: 200px;
    width: 200px;
  }
</style>

<!-- <style>
	div {
		width: 300px;
		min-height: 100px;
		border: 2px solid #ddd;
		margin-top: 15px;
		display: flex;
		align-items: center;
		justify-content: center;
		font-weight: bold;
		color: #ccc;
	}
	img {
		width: 100%;
	}
</style> -->
