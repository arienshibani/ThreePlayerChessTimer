<script>
  import { onMount } from "svelte";
  let currentPlayer = 0; // Player 0, 1, 2
  const playerColors = ["WHITE", "BLACK", "RED"];


  // The default time remaining for each player is 10 minutes.
  let timeRemaining = [600, 600, 600]; 

  // Parse URL parameters and update timeRemaining, should they wish to have shorter time.
  const updateTimersFromURL = () => {
    const searchParams = new URLSearchParams(window.location.search);
    if(searchParams.has('time')){
      //set all timers to the same time
      timeRemaining = [parseInt(searchParams.get('time')), parseInt(searchParams.get('time')), parseInt(searchParams.get('time'))];
    }
    // Set individual players times.
    if (searchParams.has('p1')) {
      timeRemaining[0] = parseInt(searchParams.get('p1'));
    }
    if (searchParams.has('p2')) {
      timeRemaining[1] = parseInt(searchParams.get('p2'));
    }
    if (searchParams.has('p3')) {
      timeRemaining[2] = parseInt(searchParams.get('p3'));
    }
  };

  onMount(() => {
    window.addEventListener('keydown', handleKeyDown);
    updateTimersFromURL();

    onDestroy(() => {
      window.removeEventListener('keydown', handleKeyDown);
    });
  });

  let timers = [null, null, null]; // Individual timers for each player

  const startTurn = () => {
    timers[currentPlayer] = setInterval(() => {
      timeRemaining[currentPlayer] -= 1;

      // If time is up, clear the timer and remove him from the game.
      if (timeRemaining[currentPlayer] <= 0) {
        clearInterval(timers[currentPlayer]);

        nextPlayer();
      }
    }, 1000);
  };

  const nextPlayer = () => {
    clearInterval(timers[currentPlayer]);
    currentPlayer = (currentPlayer + 1) % 3;
    startTurn();
  };

  startTurn();

  // Global keydown event handler
  const handleKeyDown = (event) => {
    if (event.key === " ") {
      nextPlayer();
    }
  };

  onMount(() => {
    // Attach the event listener
    window.addEventListener("keydown", handleKeyDown);

    // Cleanup when the component is destroyed
    onDestroy(() => {
      window.removeEventListener("keydown", handleKeyDown);
    });
  });
</script>

<main>
  <div class="chess-clock"
>
    {#each [0, 1, 2] as playerIndex}
      <button
        style={`background-color: ${
          timeRemaining[playerIndex] <= 0 ? "red" : "white"
        }`}
        class={playerIndex === currentPlayer ? "active" : ""}
      >
        <div class="playerInfo" id={playerColors[playerIndex]}>
          <div class="playerColor">
            {playerColors[playerIndex]}
          </div>

          <div class="timeLeft">
            {Math.floor(timeRemaining[playerIndex] / 60)}:{(
              timeRemaining[playerIndex] % 60
            )
              .toString()
              .padStart(2, "0")}
          </div>
        </div>
      </button>
    {/each}
  </div>
</main>

<style>


  main {
    position: absolute;
    height: 100%;
    width: 100%;
  }


  .playerInfo{
    margin-top: 40vh;
    height: 100%;
  }

  .chess-clock {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    width: 100vw;
    overflow: hidden;
    font-size: 2rem;
  }

  .chess-clock > button {
    /* padding: 1rem; */
    border: 1px solid #ccc;
    /* border-radius: 0.5rem; */
    margin: 0 0;
    cursor: pointer;
    height: 100%;
    width: 100%;
    text-align: center;
    font-size: 200%;
    /* align-items: center; */
    /* margin: 50%, 50%; */
    /* transform: translate(-50%, -50%); */
  }


  #WHITE {
    color: black;
  }
  #BLACK {
    color: slategray;
  }
  #RED {
    color: crimson;
  }
</style>
