<script>
  import { onMount } from "svelte";
	import { fade, fly } from 'svelte/transition';

  let currentPlayer = 0;
  let timeSelected = 600;
  const playerColors = ["WHITE", "BLACK", "RED"];
  let timeRemaining = [timeSelected, timeSelected, timeSelected];
  let timers = [null, null, null]; // Individual timers for each player
  let gameOver = false;
  let gamePaused = true;

  /**
   * @name updateTimersFromURL
   * @description Parses URL parameters to set custom timers if desired.
   * 
   *  - E.g: http://localhost/?time=300 (sets all players to 5 minutes)
   *  - E.g #2: http://localhost/?p1=60&p2=120&p3=300 (player one and two have 1 and 2 minutes respectively, player 3 has 5 minutes)
   * 
  */
  const updateTimersFromURL = () => {
    const searchParams = new URLSearchParams(window.location.search);
    if (searchParams.has("time")) {
      //Set all timers to the same time
      timeRemaining = [
        parseInt(searchParams.get("time")),
        parseInt(searchParams.get("time")),
        parseInt(searchParams.get("time")),
      ];
    }
    // Set individual players times.
    if (searchParams.has("white")) {
      timeRemaining[0] = parseInt(searchParams.get("white"));
    }
    if (searchParams.has("black")) {
      timeRemaining[1] = parseInt(searchParams.get("black"));
    }
    if (searchParams.has("red")) {
      timeRemaining[2] = parseInt(searchParams.get("red"));
    }
  };

  // Start counting down the current player's time.
  const startTurn = () => {
    timers[currentPlayer] = setInterval(() => {
      // Check if current player has time left.
      if (timeRemaining[currentPlayer] >= 0) {
        // Check if any other players have time left.
        const otherPlayers = timeRemaining.filter(
          (time, index) => index !== currentPlayer
        );

        // If there is no other player with time left, the current player wins.
        if (otherPlayers.every((time) => time <= 0)) {
          clearInterval(timers[currentPlayer]);
          gameOver = true;
          return;
        }
        // If there is, decrease it by one.
        timeRemaining[currentPlayer] -= 1;
      }

      // If time is up, clear that players time.
      if (timeRemaining[currentPlayer] <= 0) {
        clearInterval(timers[currentPlayer]);
        // If any players have time left, continue with the next players timer.
        if (timeRemaining.some((time) => time > 0)) {
          nextPlayer();
        }
      }

    }, 1000);
  };

  const nextPlayer = () => {
    gamePaused = false;
    clearInterval(timers[currentPlayer]);
    currentPlayer = (currentPlayer + 1) % 3;
    startTurn();
  };

  // Global keydown event handler
  const handleKeyDown = (event) => {
    if(gameOver){
      return;
    }
    if (event.key === " ") {
      nextPlayer();
    }

    if (event.key === "Enter") {
      // Pause the timer
      startTurn();
    }
  };


  const handleTouch = (event) => {
    if(gameOver){
      return;
    }
    
    if(event.touches.length === 2){
      nextPlayer();
    }
  };

  const handleClicks = (event) => {
    //if the select element was clicked, don't do anything
    if(event.target.classList.contains("timerOptions")){
      return;
    }

    //if game is paused, unpause it
    if(gamePaused){
      gamePaused = false;
      startTurn();
      return;
    }

    if(event.target.classList.contains("pauseButton") && !gamePaused){
      gamePaused = true;
      if(timers[currentPlayer]){
        clearInterval(timers[currentPlayer]);
      }else{
        startTurn();
      }
      return;
    }

    if(gameOver){
      return;
    }

    if(event.detail === 1){
      nextPlayer();
    }
  };

  onMount(() => {
    updateTimersFromURL();
    // Attach the event listener
    window.addEventListener("keydown", handleKeyDown);
    window.addEventListener("touchstart", handleTouch);
    window.addEventListener("click", handleClicks);
  });

  const handleSelection = (event) => {
    console.log(event.target.value)
    timeSelected = event.target.value;
  };
</script>



<main in:fly={{ y: 200, duration: 2000 }}>
  <div class="chess-clock">

    {#if gameOver}
    <div class="gameOver">
      <h2>{playerColors[currentPlayer]} wins! 👑</h2>
    </div>
    {/if}

    {#each [0, 1, 2] as playerIndex}
      <div
        style={`background-image: ${
          timeRemaining[playerIndex] <= 0 ? "" : ""
        } `}
        class={playerIndex === currentPlayer && gamePaused === false ? "active" : ""}
      >
        <div class="playerInfo" id={playerColors[playerIndex]}>
          <div>
            {playerColors[playerIndex]}
          </div>

          <div class="timeLeft">
            {
              timeRemaining[playerIndex] <= 0
                ? "Time's up! ☠️"
                : `${Math.floor(timeRemaining[playerIndex] / 60)}:${(timeRemaining[playerIndex] % 60).toString().padStart(2, "0")}`
            }
          </div>
        </div>
      </div>      
    {/each}
  </div>


  <span class="settings">
    {#if !gameOver}
    <button class="pauseButton">
      {gamePaused ? " Play ▶️" : "Pause ⏸️"}
    </button>
    {/if}

    <!-- Set value of "time selected" -->
    <select class="timerOptions" name="timers" bind:value={timeSelected} on:change={handleSelection}>
      <option value="Select a time" disabled selected>⏱️</option>
      <option value="300">5m</option>
      <option value="600">10m</option>
      <option value="900">15m</option>
      <option value="1200">20m</option>
      <option value="1500">25m</option>
      <option value="1800">30m</option>
      <option value="2100">35m</option>
      <option value="2400">40m</option>
      <option value="2700">45m</option>
      <option value="3000">50m</option>
      <option value="3300">55m</option>
      <option value="3600">60m</option>
    </select>

    <!--  -->
    <a data-sveltekit-reload href="/?time={timeSelected}">
      <button class="restartButton">
        Reset ♻️
      </button>
    </a>
  </span>

</main>


<style>
  select {
    position: absolute;
    bottom: 0;
    right: 1;
    margin: 1px;
    padding: 10px;
    width: 100px;
    font-size: 1.5rem;
    border-radius: 1rem;
    border: none;
    background-color: #fff;
    cursor: pointer;
    transition: 0.5s ease-in-out;
  }

  .restartButton{
    position: absolute;
    bottom: 0;
    right: 0;
    margin: 1px;
    padding: 10px;
    font-size: 1.5rem;
    border-radius: 1rem;
    border: none;
    background-color: #fff;
    cursor: pointer;
    transition: 0.5s ease-in-out;
  }

  .pauseButton{
    position: absolute;
    bottom: 0;
    right: 0;
    left: 0;
    margin: 1px;
    padding: 10px;
    font-size: 1.5rem;
    border-radius: 1rem;
    border: none;
    background-color: #fff;
    cursor: pointer;
    transition: 0.5s ease-in-out;
  }

  h2 {
    font-size: 5vh;
    text-align: center;
  }

  main {
    font-family: sans-serif;
    display: flow;
    justify-content: center;
    align-items: center;
    height: 100%;
    width: 100%;
    transition: 0.5s ease-in-out;

  }

  .active {
    background-image: linear-gradient(83deg, rgba(34,193,195,0) 0%, rgba(10,100,500) 100%) !important;
  }

  .playerInfo{
    user-select: none;
    cursor: pointer; 
    padding: 5rem;
    margin: auto;
    width: 100%;
    height: 100%;
    font-size: 3vh;
    font-weight: bold;
  }

  .gameOver{
    user-select: none;
  }

  .timeLeft{
    font-size: 5vh;
  }

  @media only screen and (min-width: 800px) {
    main {
      padding: 0rem;
      display: flow;
    }
  }


  
  @media only screen and (max-width: 800px) {
    .playerInfo{
    user-select: none;
    cursor: pointer; 
    padding: 3.5rem;
    font-size: 4vh;
    font-weight: bold;
  }
  }

  .chess-clock {
    overflow: hidden;
    font-size: 2rem;
  }

  #WHITE {
    color: slategrey;
  }

  #BLACK {
    color: black;
  }
  #RED {
    color: crimson;
  }
</style>
