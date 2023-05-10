<style>
.task-grid {
  display: grid;
  gap: 5px;
  grid-template-columns: 3fr 3fr 1fr;
  grid-template-areas: 
    "text text text"
    "desc desc delete"
    "image image image"
  ;
  column-gap: 10px;

  border: 1px solid black;
  background-color: #81ff81;
  border-radius: 10px;
  margin: 5px;
  padding: 20px;
}

.text {
  grid-area: text;
}

.desc {
  grid-area: desc;
}

.delete {
  grid-area: delete;
}

.image {
  grid-area: image;
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
  import { createEventDispatcher, onMount } from 'svelte';
  let send_event = createEventDispatcher();

  export let task;
  export let showReplace;

  let showImage = false;
  onMount(() => {
    showImage = task.image !== "";
  });

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

<task-view class="task-grid completed={task.done}">
  <div class="text"> 
    <input type="text" bind:value={task.text} on:input={notifyOfChange} />
  </div>
  <div class="desc"> 
    <input type="desc" bind:value={task.desc} on:input={notifyOfChange} />
  </div>
  <div class="delete"> 
    <button class="delete" on:click={deleteTask}>X</button>
  </div>
  <div class="image">
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
  </div>
</task-view>