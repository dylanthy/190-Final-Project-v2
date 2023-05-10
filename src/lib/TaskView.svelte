<style>
  task-view {
    display: grid;
    grid-template: 
      "text text delete"
      "image image image"
      /auto 1fr   auto
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
}

.replace-button {
  position: absolute;
  bottom: 10px;
  right: 10px;
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

  let openFilePicker = () => {
    fileInput.click();
  };

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
  <input type="text" bind:value={task.text} on:input={notifyOfChange} />
  <button class="delete" on:click={deleteTask}>X</button>

  <div class="image-holder" on:click={openFilePicker}>
    {#if !showImage}
      <button class="choose-file-button" on:click={openFilePicker}>Choose file</button>
    {/if}
    <input type="file" accept="image/*" id="image_input" on:change={replaceImage} bind:this={fileInput} hidden />
    {#if showImage}
      <button class="replace-image-button" on:click={openFilePicker}>Replace image</button>
      <button class="delete-image-button" on:click={deleteImage}>Delete image</button>
    {/if}
    <div id="display_image" style="background-image: url('{task.image}')">
      <input type="file" accept="image/*" id="image_input" on:change={replaceImage} bind:this={fileInput} hidden />
    </div>
  </div>
</task-view>