  <style>
h1, body{ 
  text-align: center;
}
    task-list {
      display: grid;
      grid-template-columns: 1fr, 1fr;
      justify-items: center;
    }

    .add-task {
      position: sticky;
      bottom: 1%;
      left: 120%;
      transform: translate(-50%, -50%);
      background-color: #74e277;
      border-radius: 15px;
      border: 0px;
      color: white;
      padding: 20px;
      text-align: center;
      text-decoration: none;
      display: inline-block;
      font-size: 30px;
      cursor: pointer;
    }

    .add-task:hover {
      background-color: #3e8e41;
    }
  </style>


  <script>
    import { onMount } from 'svelte';
    import TaskView from "../lib/TaskView.svelte"

    let task_list = []

    let save = function(){
      localStorage.setItem('tasks', JSON.stringify(task_list))
    }

    let add_task = function(){
      task_list = [...task_list, {done: false, text: "", image: "", imageId: Math.random().toString(36).substring(7)}]
      save()
    }

    let delete_all = function(){
      if( confirm("Delete All Plant Data?")==true){
        task_list = [];
      }
      else{
        null;
      }
    }

    let deleteTask = function(event){
      let task_to_delete = event.detail.task_to_delete
      task_list = task_list.filter((t) => t !== task_to_delete)
      save()
    }

    let add_image = function(event){
      const task_upload_img = event.detail.task_upload_img;
      const image_input = document.querySelector(`#${task_upload_img.imageId}_input`);
      const display_image = document.querySelector(`#${task_upload_img.imageId}_display`);
      var uploaded_image = "";
      image_input.addEventListener("change", function(){
        const reader = new FileReader();
        reader.addEventListener("load", () => {
          uploaded_image = reader.result;
          display_image.style.backgroundImage = `url(${uploaded_image})`;
          task_upload_img.image = uploaded_image;
          save();
        });
        reader.readAsDataURL(this.files[0]);
      })
    }

    onMount(() => {
      task_list = JSON.parse(localStorage.getItem('tasks')) || []

    })
  </script>
  <body>
  <h1>Water Wizard</h1>

  <task-list>
    {#each task_list as task }
      <TaskView task={task} on:delete={deleteTask} on:uploading={add_image} on:change={save}></TaskView>
    {/each}

    <button class="add-task" on:click={add_task}>+ Add Plant</button>
    <button class="Delete-All" on:click={delete_all}>Delete All</button>
  </task-list>
  </body>