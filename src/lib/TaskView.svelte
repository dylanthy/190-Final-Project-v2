<style>
.plant-grid {
  display: grid;
  gap: 5px;
  grid-template-areas: 
    "text text text"
    "desc desc delete"
    "image image timer"
  ;
  column-gap: 10px;

  border: 1px solid black;
  background-color: #daffc4;
  border-radius: 10px;
  margin: 5px;
  padding: 20px;
}
.time-display{
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
  display: inline-block;
  font-size: 32px;
  margin: 10px;
}
.timer-button{
  text-align: center;
  background-color: #74e277;
  border-radius: 15px;
  border: 0px;
  color: white;
  margin: 3px;
  padding: 5px;
}
.timer-button:disabled{
  background-color: #a10000;
}
#timer_input{
  text-align: center;
  background-color: #00ac06;
  border-radius: 15px;
  border: 0px;
  color: white;
  margin: 3px;
  padding: 5px;
}

.timer{
  position: relative;
  grid-area: timer;
  border: 1px solid;
  background-color: white;
  border-radius: 15px;
  padding: 5px;
}

.text {
  display: flex;
  justify-content: center;
  align-items: center;
  grid-area: text;
  font-size: 1px;
  height: 32px;
  margin: 5px;
}

.text input{
  background: transparent;
  border: 0px;
  font-size: 25px;
  height: 40px;
  width: 500px;
  text-align: center;
}
.desc input{
  background: transparent;
  border: 0px;
  text-align: center;
}

.desc {
  margin: 5px;
}
.desc input {
  font-size: 16px;
  height: 30px;
}

.image {
  grid-area: image;
  height: 300px;
}
.image-holder {
  position: relative;
  width: 300px;

  height: 100%;
  border: 1px solid black;
  border-radius: 15px;
  overflow: hidden;
}
#display_image {
  width: 100%;
  height: 100%;
  background-repeat: no-repeat;
  background-position: center;
  background-size: cover;
  background-color: white;
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
.choose-file-button:hover{
  background-color: #3e8e41;
}

.delete-button {
  position: absolute;
  top: 10px;
  right: 10px;
  background-color: yellow;
  border-radius: 50%;
  border: 0px;
  color: black;
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
  height: 1fr;
  background-color: rgb(255, 73, 73);
  border-radius: 8px;
  border: 0px;
  color:#ffffff;
  text-align:center;
  text-decoration: wavy;
  font-size: 22px;
  cursor: cursor;
  grid-area: delete;
  text-align: center;
  align-content: center;
  display: flex;
  justify-content: center;
  align-items: center;
}

.delete:hover{
  background-color: #a10000;
}
</style>

<script>
  import { createEventDispatcher, onMount } from 'svelte';

  let send_event = createEventDispatcher();
  export let plant;
  export let showReplace;

  let showImage = false;
  onMount(() => {
    showImage = plant.image !== "";
  });

  let deletePlant = function() {
    send_event("delete", { plant_to_delete: plant });
  };

  let notifyOfChange = function() {
    send_event("change", { plant_to_delete: plabt });
  };

  let fileInput;

  let replaceImage = (event) => {
    const files = event.target.files;
    const reader = new FileReader();
    reader.addEventListener("load", () => {
      plant.image = reader.result;
      showImage = true;
      send_event("uploading", { plant_upload_img: plant });
    });
    reader.readAsDataURL(files[0]);
  };

  let deleteImage = () => {
    plant.image = "";
    showImage = false;
    send_event("change", { plant });
  }
  // Add timer functionality

  
  let timeInput = "00:00:00";
  let timeRemaining = 0;
  let timerId = null;
  let reclick = true;
  let startTimer = () => {
    if(reclick ==true){
    reclick = false;
    let [hours, minutes, seconds] = timeInput.split(":").map(n => parseInt(n));
    timeRemaining = hours * 3600 + minutes * 60 + seconds;

    timerId = setInterval(() => {
      timeRemaining--;

      if (timeRemaining <= 0) {
        clearInterval(timerId);
        alert("Timer done!");
        reclick = true;
      }
    }, 1000);}
    else{
      null;
    }
  };

  let stopTimer = () => {
    clearInterval(timerId);
    reclick = true;
  };

  $: timeInputValid = /^(\d{1,3}):([0-5]\d):([0-5]\d)$/.test(timeInput);

  $: timeDisplay = new Date(timeRemaining * 1000).toISOString().substr(11, 8);

</script>
<body>
<plant-view class="plant-grid completed={plant.done}">
  <div class="text"> 
    <input type="text" bind:value={plant.text} on:input={notifyOfChange} placeholder="Name your Plant" />
  </div>
  <div class="desc"> 
    <input type="desc" bind:value={plant.desc} on:input={notifyOfChange} placeholder="Name your Plant's Species"/>
  </div>

  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <div class="delete" on:click={deletePlant}> 
    <button class="delete">Delete Plant </button>
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
      <div id="display_image" style="background-image: url('{plant.image}')"></div>
    </div>
  </div>
  <div class="timer">
    <input type="text" bind:value={timeInput} id="timer_input" placeholder="DD:HH:MM:SS"/>
    <button class="timer-button" on:click={startTimer} disabled={!timeInputValid}>Start</button>
    <button class = "timer-button" on:click={stopTimer}>Stop</button>
    <div class="time-display">{timeDisplay}</div>
  </div>
</plant-view>
</body> 