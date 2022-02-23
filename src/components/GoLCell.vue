<template>
  <div @click="switchState()" :class="(alive)? 'Cell__dead' : 'Cell__alive'"></div>
</template>

<script>
export default {
  name: 'GoLCell',
  props: {
    editable: { Boolean, default: true }
  },
  data() {
    return { 
      alive: false,
      nextAlive: false,
      neighbors: [],
    }
  },

  methods: {
    switchState() {
      if (this.editable){
      this.alive = !this.alive;
      }
    },
    reset() {
      this.alive=false;
    },
    randomize(percentage) {
      this.alive= !(Math.floor(Math.random()*100/percentage));
    },
    calculateNext() {
      let neighborsAlive = 0;
      for (const neighbor of this.neighbors){
        if (neighbor.alive) {
          neighborsAlive++;
        }
      }
      
      this.nextAlive = (neighborsAlive == 3 || neighborsAlive == 2 && this.alive);
    },
    nextGeneration: function() {
      this.alive = this.nextAlive;
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .Cell__alive {
    width: 100%;
    height: 100%;
    Background: white;
    border: 1px solid rgba(0,0,0,.1);
  }
  .Cell__dead {
    width: 100%;
    height: 100%;
    Background: black;
    border: 1px solid rgba(0,0,0,.1);
  }
</style>
