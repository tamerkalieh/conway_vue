<template>
  <div id="app">
    <div class="conway__btns">
      <button @click="play($event)">Play</button>
      <button @click.prevent="next">Next</button>
      <button @click="fastMode()">Fastmode</button>
      <button @click="reloadPage()">Repeat</button>
    </div>
    <canvas></canvas>
  </div>
</template>

<script>
export default {
  name: "ILikeCoffee",
  data() {
    return {
      context: null,
      size: 20,
      width: 400,
      height: 400,
      canvasRows: 0,
      canvasCols: 0,
      autoMode: false,
      grid: [],
    };
  },
  methods: {
    initGrid() {
      return new Array(this.canvasCols)
        .fill(null)
        .map(() =>
          new Array(this.canvasRows)
            .fill(null)
            .map(() => Math.floor(Math.random() * 2))
        );
    },
    updateGrid() {
      this.grid = this.newGen(this.grid);
      this.render(this.grid);
      this.autoMode
        ? setTimeout(() => requestAnimationFrame(this.updateGrid), 1000)
        : false;
    },
    newGen(grid) {
      const newGen = grid.map((arr) => [...arr]);
      for (let col = 0; col < grid.length; col++) {
        for (let row = 0; row < grid[col].length; row++) {
          const cell = grid[col][row];
          let numNeighbours = 0;
          for (let i = -1; i < 2; i++) {
            for (let j = -1; j < 2; j++) {
              if (0 === i && 0 === j) continue;
              const x_cell = col + i;
              const y_cell = row + j;

              if (
                0 <= x_cell &&
                0 <= y_cell &&
                x_cell < this.canvasCols &&
                y_cell < this.canvasRows
              ) {
                const currentNeighbour = grid[col + i][row + j];
                numNeighbours += currentNeighbour;
              }
            }
          }

          if (
            (1 === cell && 2 > numNeighbours) ||
            (1 === cell && 3 < numNeighbours)
          )
            newGen[col][row] = 0;
          else if (0 === cell && 3 === numNeighbours) newGen[col][row] = 1;
        }
      }
      return newGen;
    },
    render(grid) {
      for (let col = 0; col < grid.length; col++) {
        for (let row = 0; row < grid[col].length; row++) {
          const cell = grid[col][row];

          this.context.beginPath();
          this.context.rect(
            col * this.size,
            row * this.size,
            this.size,
            this.size
          );
          this.context.fillStyle = cell ? "#000" : "#fff";
          this.context.fill();
          this.context.stroke();
        }
      }
    },
    play($event) {
      this.autoMode = !this.autoMode;
      $event.target.innerText = this.autoMode ? "Stop" : "Play";
      this.updateGrid();
    },
    next() {
      this.autoMode = false;
      this.updateGrid();
    },
    fastMode() {
      this.grid = this.newGen(this.grid);
      this.render(this.grid);
      requestAnimationFrame(this.fastMode);
    },
    reloadPage() {
      location.reload();
    },
  },
  mounted() {
    const canvas = document.querySelector("canvas");
    this.context = canvas.getContext("2d");
    canvas.width = this.width;
    canvas.height = this.height;
    this.canvasCols = canvas.width / this.size;
    this.canvasRows = canvas.height / this.size;
    this.grid = this.initGrid();
  },
};
</script>

<style>
body {
  display: grid;
  place-items: center;
}
.conway__btns {
  display: flex;
  margin: 1rem 0;
}

.conway__btns > button {
  margin: 0 1rem;
}
</style>
