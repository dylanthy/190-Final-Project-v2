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

  #display_image{
    height:300px;
    border: 1px solid black;
    background-size: cover;
    overflow: hidden;
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
    cursor: pointer;
  }

  .delete:hover{
    background-color: #a10000;
  }
</style>

<script>
  import { createEventDispatcher } from 'svelte'
  let send_event = createEventDispatcher()

  export let task

  let deleteTask = function(){
    send_event("delete", {task_to_delete: task})
  }

  let notifyOfChange = function(){
    send_event("change", {task_to_delete: task})
  }

  let add_image = function(event){
    const files = event.target.files;
    const reader = new FileReader();
    reader.addEventListener("load", () => {
      task.image = reader.result;
      send_event("uploading",{task_upload_img: task})
    });
    reader.readAsDataURL(files[0]);
  }
</script>

<task-view class:completed={task.done}>
  <input type="text"     bind:value={task.text}   on:input={notifyOfChange} />
  <button class="delete" on:click={deleteTask}>X</button>
  <!-- svelte-ignore a11y-missing-attribute -->
  <input type="file" id="image_input" accept="image/jpg" on:change={add_image}/>
  <div id="display_image" style="background-image: url('{task.image}')"> </div>
</task-view>