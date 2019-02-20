<template>
  <div id="app">
    <h1 class="title">Dining philosophers</h1>
    <button class="btn" @click="initDinner()">Start</button>
    <div class="container">
      <div class="states">
        <pre>
          {{ statesPhilosophers }}
        </pre>
      </div>
      <div class="table">

        <div class="container-philosopher">
          <div v-for="philosopher in philosophers" :key="philosopher.id"
            :class="`
            philosopher
            philosopher--${philosopher.className}
            ${philosopher.state === 1
              ? 'isDinnering'
              : philosopher.state === 2
              ? 'isThinking'
              : 'isWaiting'}`">
            <div :class="`info info--${philosopher.className}`">
              <label class="info__name">{{ philosopher.name }}</label>
              <label class="info__rations">{{ philosopher.rations }}</label>
            </div>
          </div>
        </div>

        <div class="container-fork">
          <div v-for="fork in forks" :key="fork.id"
            :class="`
            fork
            fork--${fork.name}
            ${fork.isOcuped ? 'isDinnering' : ''}`">
          </div>
        </div>
        <pre style="text-align: center">
          {{ g }}
        </pre>

      </div>
    </div>
  </div>
</template>

<script>
const rations = 5
const transitionTime = 2000

export default {
  name: 'app',

  data () {
    return {
      philosophers: [
        { id: 0, className: 'socrates', name: 'SÓCRATES', state: 0, forks: [0, 4], rations: rations },
        { id: 1, className: 'platon', name: 'PLATÓN', state: 0, forks: [0, 1], rations: rations },
        { id: 2, className: 'aristoteles', name: 'ARISTÓTELES', state: 0, forks: [1, 2], rations: rations },
        { id: 3, className: 'heraclito', name: 'HERACLITO', state: 0, forks: [2, 3], rations: rations },
        { id: 4, className: 'pitagoras', name: 'PITÁGORAS', state: 0, forks: [3, 4], rations: rations }
      ],
      forks: [
        { id: 0, name: 'one', isOcuped: false },
        { id: 1, name: 'two', isOcuped: false },
        { id: 2, name: 'three', isOcuped: false },
        { id: 3, name: 'four', isOcuped: false },
        { id: 4, name: 'five', isOcuped: false }
      ],
      states: ['isDinnering', 'isWaiting', 'isSated'],
      x: 0,
      y: 0,
      lastOne: 0,
      lastTwo: 0,
      flag: false
    }
  },

  computed: {
    g () {
      return `${this.x}  ${this.y}`
    },
    statesPhilosophers () {
      return this.philosophers[0].state
    }
  },


  methods: {
    initDinner () {
      let that = this
      async function wait () {
        return new Promise((resolve) => {
          console.log('lastOne ', that.lastOne)
          console.log('lastTwo ', that.lastTwo)
          setTimeout(resolve, transitionTime)
        })
      }

      (async () => {
        while (true) {
          await wait()
          this.getPartner()
          // this.philosophers[this.lastOne].state = 2
          // this.philosophers[this.lastTwo].state = 2
        }
      })()
    },

    initx () {
      for (let index = 0; index < 20; index++) {
        (() => {
          this.getPartner()
        })(index)
      }
    },

    init () {
      this.philosophers[3].state = 1
      this.forks[ this.philosophers[3].forks[0] ].isOcuped = true
      this.forks[ this.philosophers[3].forks[1] ].isOcuped = true
    },

    changeState (index, state) {
      this.philosophers[index].state = 0
      this.philosophers[index].rations--
      this.forks[ this.philosophers[index].forks[0] ].isOcuped = false
      this.forks[ this.philosophers[index].forks[1] ].isOcuped = false
      console.log(this.philosophers[index].rations)
    },

    dinnering (x, y) {
      for (let index = 0; index < this.philosophers.length; index++) {
        this.philosophers[index].state = 0
        this.forks[ this.philosophers[index].forks[0] ].isOcuped = false
        this.forks[ this.philosophers[index].forks[1] ].isOcuped = false
      }
      this.philosophers[x].state = 1
      this.discountRation(x)
      this.forks[ this.philosophers[x].forks[0] ].isOcuped = true
      this.forks[ this.philosophers[x].forks[1] ].isOcuped = true
      this.philosophers[y].state = 1
      this.discountRation(y)
      this.forks[ this.philosophers[y].forks[0] ].isOcuped = true
      this.forks[ this.philosophers[y].forks[1] ].isOcuped = true

      if (this.flag) {
        if (x === 0) x = 4
        else x--
        if (y === 0) y = 4
        else y--
        this.philosophers[x].state = 2
        this.philosophers[y].state = 2
      }
      this.flag = true
    },

    clean (id) {
      this.philosophers[id].state = 0
      this.forks[ this.philosophers[id].forks[0] ].isOcuped = false
      this.forks[ this.philosophers[id].forks[1] ].isOcuped = false
    },

    getPartner () {
      if (this.x > 2) this.y = this.x - 3
      else this.y = this.x + 2
      this.dinnering(this.x, this.y)
      this.x++
      if (this.x === 5) this.x = 0
    },

    discountRation (index) {
      this.philosophers[index].rations--
    }
  }
}
</script>

<style lang="scss">
$tableSize: 28em;
$tableColor: gray;
$philosopherSize: 9em;
$philosopherColor: white;
$forkSize: 3em;
$forkColor: white;

$baseRotation: 72;

$mapPhilosophers: (
  socrates, platon, aristoteles, heraclito, pitagoras
);

$mapNumbers: (
  one, two, three, four, five
);

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  display: grid;
  grid-gap: 1em;
  justify-content: center;
}

.container {
  display: grid;
  grid-template-columns: 1fr 3fr;
  margin: 0 auto;
}

.table {
  position: relative;
  background-color: $tableColor;
  width: $tableSize;
  height: $tableSize;
  margin: auto;
  border-radius: 50%;
}

.container-fork {
  height: $tableSize;
  width: $tableSize;
  position: absolute;
  top: 0;
  transform: rotate(36deg);
}

.fork {
  position: absolute;
  height: $forkSize;
  width: $forkSize / 4;
  background-color: $forkColor;
  border: 1px solid black;
  top: 25%;
  left: calc(50% - #{$forkSize / 6});
  transform: translate(0, 0);
  //left: calc(50% - (#{$forkSize / 5}));
  transform-origin: center 7em;
  &::before {
    content: "";
    display: block;
    background-color: $forkColor;
    height: 1em;
    width: 1.1em;
    border: 1px solid black;
    margin-left: -4px;
    transform: rotate(90deg) translate(200%, 0);
  }
}

.container-philosopher {
  height: $tableSize;
  width: $tableSize;
  position: relative;
}

.philosopher {
  position: absolute;
  height: $philosopherSize;
  width: $philosopherSize;
  background-color: $philosopherColor;
  border-radius: 50%;
  border: 1px dashed black;
  top: 0;
  left: calc(50% - (#{$philosopherSize / 2}));
  transform: translate(-50%, 0);
  transform-origin: center 14em;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

@mixin itemPosition($className, $map) {
  $i: 0;
  @each $name in $map {
    .#{$className}--#{$name} {
      transform: rotate(#{$i * $baseRotation}deg);
    }
    $i: $i + 1;
  }
}

@mixin fixInfoPosition() {
  $i: 0;
  @each $name in $mapPhilosophers {
    .info--#{$name} {
      transform: rotate(-#{$i * $baseRotation}deg);
    }
    $i: $i + 1;
  }
}

@include itemPosition(philosopher, $mapPhilosophers);
@include itemPosition(fork, $mapNumbers);
@include fixInfoPosition();

.info {
  display: flex;
  flex-direction: column;
  align-items: center;
  font-weight: 900;
  &__name {
    //
  }
  &__rations {
    font-size: 1.5em;
  }
}

.isDinnering {
  background-color: green;
  transition: all 1s;
}
.isThinking {
  background-color: orange;
  transition: all 1s;
}
.isWaiting {
  background-color: red;
  transition: all 1s;
}
.nothing {
  background-color: whitesmoke;
  transition: all 1s;
}

.btn {
  // margin-top: 1em;
  justify-self: center;
  background-color: tomato;
  padding: 1em 2em;
  color: white;
  display: block;
  cursor: pointer;
  z-index: 10;
}
.title {
  justify-self: center;
  margin: 0;
}
</style>
