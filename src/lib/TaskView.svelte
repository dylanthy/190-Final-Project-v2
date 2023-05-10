<style>
.task-grid {
  display: grid;
  gap: 5px;
  grid-template-columns: 5fr 6fr 1fr;
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

.timer{
  grid-area: timer;
  border: 1px solid
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
  font-size: 25px;
  height: 40px;
  width: 500px;
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
}
.image-holder {
  position: relative;
  width: 300px;
  height: 300px;
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
  height: 1fr;
  background-color: rgb(255, 73, 73);
  border-radius: 8px;
  border: 0px;
  color:#ffffff;
  text-align:center;
  text-decoration: wavy;
  font-size: 32px;
  cursor: cursor;
  grid-area: delete;
  text-align: center;
  align-content: center;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size:40px;
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
  }
  // Add timer functionality
  let timeInput = "00:00:00:00";
  let timeRemaining = 0;
  let timerId = null;

  let startTimer = () => {
  let [days, hours, minutes, seconds] = timeInput.split(":").map(n => parseInt(n));
  timeRemaining = days * 86400 + hours * 3600 + minutes * 60 + seconds;

    timerId = setInterval(() => {
      timeRemaining--;

      if (timeRemaining <= 0) {
        clearInterval(timerId);
        alert(task.text + " Needs to Be Watered!");
      }

      let days = Math.floor(timeRemaining / 86400);
      let hours = Math.floor((timeRemaining % 86400) / 3600);
      let minutes = Math.floor((timeRemaining % 3600) / 60);
      let seconds = timeRemaining % 60;

      timeDisplay = `${days.toString().padStart(2, '0')}:${hours.toString().padStart(2, '0')}:${minutes.toString().padStart(2, '0')}:${seconds.toString().padStart(2, '0')}`;
    }, 1000);
  };

  let stopTimer = () => {
    clearInterval(timerId);
  };

$: timeInputValid = /^(\d{1,2}):(\d{1,2}):(\d{1,2}):(\d{1,2})$/.test(timeInput);

$: timeDisplay = timeInputValid ? timeInput : "00:00:00:00";

</script>

<task-view class="task-grid completed={task.done}">
  <div class="text"> 
    <input type="text" bind:value={task.text} on:input={notifyOfChange} placeholder="Name your Plant" />
  </div>
  <div class="desc"> 
    <input type="desc" bind:value={task.desc} on:input={notifyOfChange} placeholder="Name your Plant's Species"/>
  </div>

  <!-- svelte-ignore a11y-click-events-have-key-events -->
  <div class="delete" on:click={deleteTask}> 
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
      <div id="display_image" style="background-image: url('{task.image}')"></div>
    </div>
  </div>
  <div class="timer">
    <input type="text" bind:value={timeInput} id="timer_input" placeholder="DD:HH:MM:SS"/>
    <button on:click={startTimer} disabled={!timeInputValid}>Start</button>
    <button on:click={stopTimer}>Stop</button>
    <div class="time-display">{timeDisplay}</div>
  </div>
</task-view>