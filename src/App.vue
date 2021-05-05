<template>
  <link
    href="https://fonts.googleapis.com/css?family=Amatic+SC:400,700"
    rel="stylesheet"
    type="text/css"
  />
  <div class="headingDiv">
    <h1>
      Whack-a-mole!
      <span class="score" ref="scoreBoard">{{ score }}</span
      ><span v-if="end">/ {{ numberOfMoles }}</span>
    </h1>
    <button @click="startGame()" class="startBtn">Lets play!</button>
  </div>

  <div class="game">
    <div
      v-for="(hole, i) in 6"
      :key="i"
      :class="`hole hole${i + 1}`"
      :ref="
        (el) => {
          holes[i] = el;
        }
      "
    >
      <div @click="bonk($event)" class="mole" ref="mole"></div>
    </div>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, Ref, ref } from "vue";

export default defineComponent({
  name: "App",
  setup() {
    const holes: Ref = ref([]);
    const scoreBoard: any = ref(null);
    const moles: any = computed(() => {
      return document.getElementsByClassName("mole");
    });

    let lastHole: any;
    let timeUp = false;
    let score = ref(0);
    let numberOfMoles = ref(0);
    let end = ref(false);

    return {
      holes,
      scoreBoard,
      moles,
      lastHole,
      timeUp,
      score,
      numberOfMoles,
      end,
    };
  },

  data() {
    return {};
  },

  methods: {
    // Creates a random time between the set min max range
    randomTime(min: number, max: number): number {
      return Math.round(Math.random() * (max - min) + min);
    },
    // Function that gives a random hole from the holes list of divs. Param input is holes list.
    randomHole(holes: NodeList): Element {
      const index: number = Math.round(Math.random() * holes.length);
      const hole: any = holes[index];
      if (hole === this.lastHole) {
        // This prevents the hole that is generated to be repeated. If hole is same as previous selected Hole rerun function.
        // The return will exit the function in the if but if the if is not used the bigger function will return the hole.
        return this.randomHole(holes);
      }
      this.lastHole = hole;
      return hole;
    },
    // Function that makes the moles pop up
    peep() {
      const time = this.randomTime(200, 1000);
      const hole = this.randomHole(this.holes);
      hole.classList.add("up");
      setTimeout(() => {
        hole.classList.remove("up");
        if (!this.timeUp) this.peep();
      }, time);
      this.numberOfMoles++;
    },
    // Function to start the game and reset score
    startGame() {
      this.scoreBoard.textContent = "0";
      this.timeUp = false;
      this.score = 0;
      this.numberOfMoles = 0;
      this.end = false;
      this.peep();
      setTimeout(() => {
        this.timeUp = true;
        this.end = true;
      }, 10000);
    },
    // Function that bonks the mole on the head and adds point to score
    bonk(event: any) {
      if (!event.isTrusted) return; // This check if the user really clicked the mole or was it fake. aka cheater
      this.score++;
      event.srcElement.classList.remove("up");
    },
  },
});
</script>

<style>
html {
  box-sizing: border-box;
  font-size: 10px;
  background: #ffc600;
}

*,
*:before,
*:after {
  box-sizing: inherit;
}

body {
  padding: 0;
  margin: 0;
  font-family: "Amatic SC", cursive;
}

.headingDiv {
  text-align: center;
}

h1 {
  text-align: center;
  font-size: 10rem;
  line-height: 1;
  margin-bottom: 0;
}

.score {
  background: rgba(255, 255, 255, 0.2);
  padding: 0 3rem;
  line-height: 1;
  border-radius: 1rem;
}

.startBtn {
  margin-top: 3rem;
  display: inline-block;
  padding: 0.3em 1.2em;
  border-radius: 2em;
  box-sizing: border-box;
  text-decoration: none;
  color: #ffffff;
  background-color: #333333;
  text-align: center;
  transition: all 0.2s;
  border: none;
  width: 10rem;
  height: 4rem;
  font-family: "Amatic SC", cursive;
  font-size: 2rem;
}

.startBtn:hover {
  background-color: #333333;
}

.game {
  width: 600px;
  height: 400px;
  display: flex;
  flex-wrap: wrap;
  margin: 0 auto;
}

.hole {
  flex: 1 0 33.33%;
  overflow: hidden;
  position: relative;
}

.hole:after {
  display: block;
  background: url("./assets/dirt.svg") bottom center no-repeat;
  background-size: contain;
  content: "";
  width: 100%;
  height: 70px;
  position: absolute;
  z-index: 2;
  bottom: -30px;
}

.mole {
  background: url("./assets/mole.svg") bottom center no-repeat;
  background-size: 60%;
  position: absolute;
  top: 100%;
  width: 100%;
  height: 100%;
  transition: all 0.4s;
}

.hole.up .mole {
  top: 0;
}
</style>
