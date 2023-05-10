<style>
  task-grid {
    display: grid;
    gap: 5px;
    grid-template-columns: 3fr 3fr 1fr;
	  grid-template-areas: 
      "delete text test"
      "desc desc delete"
      "image image image"
      5fr 
    ;

    column-gap: 10px;
  }

.image-holder {
  position: relative;
  width: 300px;
  height: 300px;
  border: 1px solid black;
  overflow: hidden;
}

#display_image {
  width: 100%;
  height: 100%;
  background-repeat: no-repeat;
  background-position: center;
  background-size: cover;
}

.choose-file-button {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #74e277;
  border-radius: 15px;
  border: 0px;
  color: white;
  padding: 10px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 10px;
  cursor: pointer;
}

.delete-button {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: yellow;
  border-radius: 50%;
  border: 0px;
  color: black;
  padding: 5px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 20px;
  cursor: pointer;
  display: none;
}

.replace-button {
  position: absolute;
  bottom: 5px;
  right: 5px;
  background-color: #74e277;
  border-radius: 10px;
  border: 0px;
  color: white;
  padding: 4px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 1px;
  cursor: pointer;
}

.delete{
  background-color: rgb(255, 73, 73);
  border-radius: 8px;
  border: 0px;
  color:#ffffff;
  padding: 10px;
  text-align:center;
  text-decoration: wavy;
  font-size: 16;
  cursor: cursor;
}

.delete:hover{
  background-color: #a10000;
}
</style>

<script>
  let showImage = false;

  import { createEventDispatcher } from 'svelte';
  let send_event = createEventDispatcher();

  export let task;

  let deleteTask = function() {
    send_event("delete", { task_to_delete: task });
  };

  let notifyOfChange = function() {
    send_event("change", { task_to_delete: task });
  };

  let fileInput;

  let replaceImage = (event) => {
    const files = event.target.files;
    const reader = new FileReader();
    reader.addEventListener("load", () => {
      task.image = reader.result;
      showImage = true;
      send_event("uploading", { task_upload_img: task });
    });
    reader.readAsDataURL(files[0]);
  };

  let deleteImage = () => {
    task.image = "";
    showImage = false;
    send_event("change", { task });
  };
</script>

<task-view class:completed={task.done}>
  <input type="desc" bind:value={task.desc} on:input={notifyOfChange} />
  <input type="text" bind:value={task.text} on:input={notifyOfChange} />
  <button class="delete" on:click={deleteTask}>X</button>

  <div class="image-holder">
    {#if !showImage}
      <button class="choose-file-button" on:click={() => fileInput.click()}>Choose file</button>
    {/if}
    <input type="file" accept="image/*" id="image_input" on:change={replaceImage} bind:this={fileInput} hidden />
    {#if showImage}
      <button class="replace-button" on:click={() => fileInput.click()}>Replace</button>
      <button class="delete-button" on:click={deleteImage}>Delete image</button>
    {/if}
    <div id="display_image" style="background-image: url('{task.image}')"></div>
  </div>
</task-view>