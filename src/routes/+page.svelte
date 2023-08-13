<script>
  import { onMount } from "svelte";
	import { tweened } from 'svelte/motion';


  let currentPlayer = 0;
  const playerColors = ["WHITE", "BLACK", "RED"];
  let timeRemaining = [600, 600, 600];
  let timers = [null, null, null]; // Individual timers for each player
  let gameOver = false;


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
    if (searchParams.has("p1")) {
      timeRemaining[0] = parseInt(searchParams.get("p1"));
    }
    if (searchParams.has("p2")) {
      timeRemaining[1] = parseInt(searchParams.get("p2"));
    }
    if (searchParams.has("p3")) {
      timeRemaining[2] = parseInt(searchParams.get("p3"));
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
</script>

<main>
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
        class={playerIndex === currentPlayer ? "active" : ""}
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


</main>

<style>
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

  .chess-clock {
    overflow: hidden;
    font-size: 2rem;
  }

  .chess-clock > button {
    border: 1px solid #ccc;
    cursor: pointer;
    text-align: center;
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
