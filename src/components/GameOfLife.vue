<template>
  <div>
    <div class="header">
      <h1> Game Of Life </h1>
    </div>
    <div :style="gridStyle" class="game">
      <div v-for="index in size*size" :key="index">
        <GoLCell ref="Cells" :editable="editMode"></GoLCell>
      </div>
    </div>
    <div class="controlls">
    <label for="size">Anzahl(Zellen pro Achse):</label>
    <input type="number" min="1" max="200" v-model="sizeSlider" v-on:change="sizeChanged">
    </div>
    <div class="controlls">
    <label for="size">Random lebende Zellen (in %):</label>
    <input type="number" v-model="percentAlive">
    </div>
    <div class="controlls">
    <label>Das Spiel ist: {{started ? 'Gestartet' : 'Gestopt'}}</label>
    </div>
    <div class="buttons">
      <button type="button" @click="randomizeClick">Randomize</button>
      <button type="reset" @click="resetClick">Reset</button>
      <button type="submit" @click="startClick">Start/Stop</button>
    </div>
  </div>
</template>

<script>
import GoLCell from './GoLCell.vue'

export default {
  name: 'GameOfLife',
  components: {
    GoLCell
  },
  data() {
    return {
      editMode: true,
      started: false,
      sizeChange: false,
      size: 20,
      sizeSlider: 20,
      percentAlive: 25,
      cells: [],
      sizeChangeTimeout: null
    }
  },
  computed: {
    gridStyle() {
      return {
        gridTemplateColumns: 'repeat(' + this.size + ', 1fr)'
      }
    },
  },
  mounted(){
    this.sizeSlider = this.size;
    this.cells = [];
    this.cells = this.$refs.Cells.slice();
    this.linkNeighbors();
  },
  methods: {
    randomizeClick() {
      this.cells.forEach((cell) => cell.randomize(this.percentAlive));
    },
    resetClick() {
      this.cells.forEach((cell) => cell.reset());
    },
    startClick() {
      if (this.started) {
        this.editMode = true;
        this.started = false;
      } else {
        this.editMode = false;
        this.started = true;
        this.run();

      }
    },
    sizeChanged() {
      this.sizeChange = true;
      if (this.sizeChange) {
        clearTimeout(this.sizeChangeTimeout);
      }
      this.sizeChangeTimeout = setTimeout(() => {
        this.size = parseInt(this.sizeSlider);

        this.$nextTick(() => this.linkNeighbors());
        this.sizeChange = false;
      }, 1000);
    },
    async run() {
      let sleep = ms => new Promise(resolve => setTimeout(resolve, ms));
      
      while(this.started) {
        this.cells.forEach((cell) => cell.calculateNext());
        await sleep(500);
        this.cells.forEach((cell) => cell.nextGeneration());
      }
    },
    linkNeighbors() {
      this.cells = [];
      this.$refs.Cells.forEach(cell => this.cells.push(cell));

      const ElementCount = this.size*this.size;
      for (let i = 0; i < ElementCount; i++) {
        const neighbors = [];
        if (i - this.size - 1 >= 0 && i % this.size) neighbors.push(this.cells[i - this.size - 1]);
        if (i - this.size >= 0) neighbors.push(this.cells[i - this.size]);
        if (i - this.size + 1 >= 0 && (i + 1) % this.size) neighbors.push(this.cells[i - this.size + 1]);

        if (i - 1 >= 0 && i % this.size) neighbors.push(this.cells[i - 1]);
        if (i + 1 < ElementCount && (i + 1) % this.size) neighbors.push(this.cells[i + 1]);

        if (i + this.size - 1 < ElementCount && i % this.size) neighbors.push(this.cells[i + this.size - 1]);
        if (i + this.size < ElementCount) neighbors.push(this.cells[i + this.size]);
        if (i + this.size + 1 < ElementCount && (i + 1) % this.size) neighbors.push(this.cells[i + this.size + 1]);
        this.cells[i].neighbors = neighbors;
      }
    },
  },
}

</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .header {
    display: flex;
    justify-content: center;
  }

  .game {
    width: 45rem;
    height: 45rem;
    margin: auto;
    border: 2px solid rgba(0,0,0,.2);
    display: grid;
    grid-gap: 1px;
    grid-template-columns: var(gridTemplateColumns);
  }

  .controlls {
    width: 45rem;
    margin: auto;
  }
  
  .label {
    width: 10rem;      
  }

  .buttons {
    display: flex;
    justify-content: center;
  }
</style>
