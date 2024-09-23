<script>
  import { onMount } from 'svelte';

  let screenTimeImage = "./src/assets/screentimeimage.jpg"
  let screenTimes = [];
  let device = '';
  let hours = 0;
  let minutes = 0;
  let isClicked = false;
  let limitHours = 0;
  let limitMinutes = 0;
  let limitTime = 0;
  let limitStatus = "";
  let percent = 0;
  let todaysDate = new Date();
  let currentDate;
  let currentTime;
  let totalMinutes = 0;
  let selectedDate = new Date().toISOString().split('T')[0];
  let entriesByDate = {};
  let journalEntries = [];
  let pastEntries = ["hi","buh","cuh","fuh"];
  let newEntry = '';

  function addScreenTime() {
    if (device && (hours > 0 || minutes > 0)) {
      if (minutes == null) minutes = 0;
      screenTimes = [...screenTimes, { device, hours, minutes }];
      totalMinutes += + minutes + 60*hours
      device = '';
      hours = 0;
      minutes = 0;
    }
    checkLimit();
  }

  function setLimit() {
    limitTime = limitMinutes + 60* limitHours;
    limitHours = 0;
    limitMinutes = 0;
    checkLimit();
  }

  function checkLimit() {
    percent = (totalMinutes/limitTime)*100; 
    if (limitTime > totalMinutes) limitStatus = "On Track For Today's Limit"
    else if (limitTime == totalMinutes) limitStatus = "You're At Your Screen Time Limit"
    else limitStatus = "Limit Not Met"
  }

  function toggleColors() {
    isClicked = !isClicked;
    if (isClicked) {
      document.body.style.backgroundColor = 'white';
      document.body.style.color = 'black';
    } else {
      document.body.style.backgroundColor = '';
      document.body.style.color = '';
    }
  }

  function addJournalEntry() {
    if (newEntry.trim()) {
      journalEntries = [...journalEntries, newEntry];
      newEntry = '';
    }
  }

  function formatDate(date) {
    return date.toISOString().split('T')[0];
  }

  function changeDate(days) {
    todaysDate.setDate(todaysDate.getDate() + days);
    todaysDate = new Date(todaysDate); // Update the date object
    formatDate(todaysDate);
  }

  onMount(() => {
    const date = new Date();
    currentDate = date.toLocaleDateString();
    currentTime = date.toLocaleTimeString();
  });
</script>

<main>
  <div class="header">Screen Time Tracking<br><div class="date">
  {currentDate + " " + currentTime}
  </div>
  </div>

<div class="form">
  <div>
    <h2>Screen Time Input</h2>
  <input type="text" class="screentype" placeholder="Device" bind:value={device} />
  <input type="number" class= "numberinput" placeholder="Hours" bind:value={hours} min="0" />
  <input type="number" class="numberinput" placeholder="Minutes" bind:value={minutes} min="0" /><br><br>
  <button on:click={addScreenTime}>Add Screen Time</button><br></div>

  <div>
  <h2>Journal</h2>
  <input type="text" class="journal"
    placeholder="Write your journal entry here" 
    bind:value={newEntry} />
  <br /><br>
  <button on:click={addJournalEntry}>Submit Journal</button></div>
</div>

<div class="screen-time-list">
  <h3>Tracked Screen Time</h3>
    {#each screenTimes as { device, hours, minutes }}
      <li>{device}: {hours}h {minutes}m</li>
    {/each}
  <h3>Total Screen Time Today: {totalMinutes} minutes</h3>
  <h3>Screen Time Limit: {limitTime} minutes</h3>
  <h3>{"You've used " + percent.toPrecision(3) + "% of today's limit" }</h3>
</div>
<div class="journal-list">
  <h3>Journal Entries</h3>
    {#each journalEntries as entry}
      <li>{entry}</li>
    {/each}
</div>
<div class="pastentries">
  <div class="date-container">
    <button class="button" on:click={() => changeDate(-1)}>Prev Day</button>
    <div class="date">{formatDate(todaysDate)}</div>
    <button class="button" on:click={() => changeDate(1)}>Next Day</button>
  </div>
  <h2>{#each pastEntries as entry}
    <li>{entry}</li>
  {/each}</h2>
</div>
  
<div class="limits">
  <div>
  <h2>Set Daily Limit For The Week<br></h2>
  <input type="number" placeholder="Limit Hours" bind:value={limitHours} min="0" />
  <input type="number" placeholder="Limit Minutes" bind:value={limitMinutes} min="0" />
  <h3><button on:click={setLimit}>Set Limit</button></h3>
</div></div>

<button class="button" on:click={toggleColors}>
  Dark/Light
</button>

</main>

<style>
  .header {
    margin-bottom: 1em;
    flex-direction: column;
    display: flex;
    font-size: xx-large;
    font-family: 'Times New Roman', Times, serif;
  }
  .form {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    width: 80em;
    height:30em;
    margin-bottom: 20px;
    border-width: 10px;
    border-color: white;
    background-color: #646cffaa;
    border-radius: 25px;
  }
  .pastentries {
    display: flex;
    flex-direction: column;
    float: left;
    align-items: center;
    justify-content: center;
    width: 39em;
    height:30em;
    margin-bottom: 20px;
    border-width: 10px;
    border-color: white;
    background-color: #3e439eaa;
    border-radius: 25px;
    padding-right: .5em;
  }
  .limits {
    display: flex;
    flex-direction: column;
    float: right;
    align-items: center;
    justify-content: center;
    width: 39em;
    height:30em;
    margin-bottom: 20px;
    border-width: 10px;
    border-color: white;
    background-color: #3e439eaa;
    border-radius: 25px;
    padding-left: .5em;
  }
  .screen-time-list {
    margin-top: 20px;
    background-color: rgb(41, 116, 116);
    border-radius: 25px;
  }
  .journal-list {
    margin-top: 20px;
    background-color: rgb(41, 116, 116);
    border-radius: 25px;
    margin-bottom: 1em;
  }
  .button {
    padding: 10px 20px;
    border: none;
    cursor: pointer;
    font-size: 16px;
  }
  .screentype {
    height: 5em;
    border-radius: 5px;
    text-align: center;
  }
  .numberinput {
    height: 5em;
    border-radius: 5px;
    text-align: center;
  }
  .journal {
    height: 5em;
    border-radius: 5px;
    width: 14em;
    text-align: center;
  }
  .date-container {
    display: flex;
    align-items: center;
    gap: 10px;
  }
  .date {
    font-size: 1.5em;
  }
</style>