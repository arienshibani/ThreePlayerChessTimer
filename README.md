# 3 Player Chess Timer ♚

Someone brought in a three player chessboard at the office so I made my own chess timer for it.

## Live Demo
<https://three-player-chess-timer.vercel.app>

![image](https://github.com/arienshibani/ThreePlayerChessTimer/assets/22197324/5e0b7848-3829-4609-8d61-11711b6682e1)



## Features
* Customizable timers through the UI, or directly in the browser through query parameters.
* Touch / Click anywhere to cycle to the next player and pause the last timer.
* Pause / Unpause button

### Query Parameters
* `?time` to set the time for all three players
   * For instance, a 20 second game can be configured as such <https://three-player-chess-timer.vercel.app/?time=20>
* `?white/black/red` individual player colors to set asynchronous timers 
  * For instance  <https://three-player-chess-timer.vercel.app/?white=10&black=20&red=300>

