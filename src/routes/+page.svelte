<script>
     import { onMount } from 'svelte';
    let currentPlayer = 0; // Player 0, 1, 2
    let timeRemaining = [600, 600, 600]; // 10 minutes in seconds for each player
    const playerColors = ["white", "black", "red"]

    let timers = [null, null, null]; // Individual timers for each player
  
    const startTurn = () => {
      timers[currentPlayer] = setInterval(() => {
        timeRemaining[currentPlayer] -= 1;
  
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
        if (event.key === ' ') {
        nextPlayer();
        }
    };

    onMount(() => {
        // Attach the event listener
        window.addEventListener('keydown', handleKeyDown);

        // Cleanup when the component is destroyed
        onDestroy(() => {
            window.removeEventListener('keydown', handleKeyDown);
        });
    });

  </script>
  
  <style>
    html {
        margin: 0;
        background-color: bisque;
    }
    main {
      position: absolute;
      height: 100%;
      width: 100%;
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

    .chess-clock > div {
      /* padding: 1rem; */
      border: 1px solid #ccc;
      /* border-radius: 0.5rem; */
      margin: 0 0;
      cursor: pointer;
      height: 100%;
      width: 100%;
      text-align: center;
      /* align-items: center; */
      /* margin: 50%, 50%; */
      /* transform: translate(-50%, -50%); */
    
    }
    
  </style>
  <main style="background-color: bisque;"
    on:click={() => playerIndex === currentPlayer && nextPlayer()}
    >
    <div class="chess-clock">
        {#each [0, 1, 2] as playerIndex}
          <div
            style={`background-color: ${timeRemaining[playerIndex] <= 0 ? 'red' : 'white'}`}
            class={playerIndex === currentPlayer ? 'active' : ''}
            on:click={() => playerIndex === currentPlayer && nextPlayer()}
          >
            {playerColors[playerIndex]}: {Math.floor(timeRemaining[playerIndex] / 60)}:{(timeRemaining[playerIndex] % 60)
              .toString()
              .padStart(2, '0')}
          </div>
        {/each}
      </div>
  </main>
