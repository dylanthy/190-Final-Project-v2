  <style>
h1, body{ 
  text-align: center;
  Font-family: 'Arial';
  font-style:bold;
  font-size: 40px;
}
.delete-all{
  position: sticky;
      bottom: 1%;
      left: 150%;
      transform: translate(-50%, -50%);
      background-color:crimson;
      border-radius: 15px;
      border: 0px;
      color: white;
      padding: 20px;
      text-align: center;
      text-decoration: none;
      display: inline-block;
      font-size: 30px;
      cursor: pointer;
      box-shadow: rgba(0, 0, 0, 0.20) 0px 4px 4px;
    }
    plant-list {
      display: grid;
      grid-template-columns: 1fr, 1fr;
      justify-items: center;
      
    }

    .add-plant {
      position: sticky;
      top: 87%;
      right: 85%;
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
      box-shadow: rgba(0, 0, 0, 0.20) 0px 4px 4px;
    }
    

    .add-plant:hover {
      background-color: #3e8e41;
    }
  </style>


  <script>
    import { onMount } from 'svelte';
    import PlantView from "../lib/PlantView.svelte"

    let plant_list = []

    let save = function(){
      localStorage.setItem('plants', JSON.stringify(plant_list))
    }

    let add_plant = function(){
      plant_list = [...plant_list, {done: false, text: "", image: "", imageId: Math.random().toString(36).substring(7)}]
      save()
    }

    let delete_all = function(){
      if( confirm("Delete All Plant Data?")==true){
        plant_list = [];
      }
      else{
        null;
      }
    }

    let deletePlant = function(event){
      let plant_to_delete = event.detail.plant_to_delete
      plant_list = plant_list.filter((t) => t !== plant_to_delete)
      save()
    }

    let add_image = function(event){
      const plant_upload_img = event.detail.plant_upload_img;
      const image_input = document.querySelector(`#${plant_upload_img.imageId}_input`);
      const display_image = document.querySelector(`#${plant_upload_img.imageId}_display`);
      var uploaded_image = "";
      image_input.addEventListener("change", function(){
        const reader = new FileReader();
        reader.addEventListener("load", () => {
          uploaded_image = reader.result;
          display_image.style.backgroundImage = `url(${uploaded_image})`;
          plant_upload_img.image = uploaded_image;
          save();
        });
        reader.readAsDataURL(this.files[0]);
      })
    }

    onMount(() => {
      plant_list = JSON.parse(localStorage.getItem('plants')) || []

    })
  </script>
  <body>
  <h1>Water Wizard</h1>

  <plant-list>
    {#each plant_list as plant }
      <PlantView plant={plant} on:delete={deletePlant} on:uploading={add_image} on:change={save}></PlantView>
    {/each}

    <button class="add-plant" on:click={add_plant}>+ Add Plant</button>
    <button class="delete-all" on:click={delete_all}>Delete All</button>
  </plant-list>
  </body>