<template>
  <div id="app">
    <h1 class="title">Dining philosophers</h1>
    <button class="btn" @click="initDinner();"><h2>START</h2></button>
    <div class="container">
      <div class="state-indicator">
        <h2 class="state-indicator__info">dinnering</h2>
        <h2 class="state-indicator__info">thinking</h2>
        <h2 class="state-indicator__info">waiting</h2>
      </div>
      <div class="table-rounded">
        <div class="container-philosopher">
          <div
            v-for="(philosopher, index) in philosophers"
            :key="index"
            :class="
              `
            philosopher
            philosopher--${philosopher.className}
            ${
              philosopher.state === 1
                ? 'isDinnering'
                : philosopher.state === 2
                ? 'isThinking'
                : 'isWaiting'
            }`
            "
          >
            <div :class="`info info--${philosopher.className}`">
              <label class="info__name">{{ philosopher.name }}</label>
            </div>
          </div>
        </div>

        <div class="container-fork">
          <div
            v-for="(fork, index) in forks"
            :key="index"
            :class="
              `
            fork
            fork--${fork.name}
            ${fork.isOcuped ? 'isDinnering' : ''}`
            "
          >
            {{ index }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const transitionTime = 2000;

export default {
  name: "app",

  data() {
    return {
      philosophers: [
        {
          className: "socrates",
          name: "SÓCRATES",
          state: 0,
          forks: [0, 4]
        },
        {
          className: "platon",
          name: "PLATÓN",
          state: 0,
          forks: [0, 1]
        },
        {
          className: "aristoteles",
          name: "ARISTÓTELES",
          state: 0,
          forks: [1, 2]
        },
        {
          className: "heraclito",
          name: "HERACLITO",
          state: 0,
          forks: [2, 3]
        },
        {
          className: "pitagoras",
          name: "PITÁGORAS",
          state: 0,
          forks: [3, 4]
        }
      ],
      forks: [
        { name: "one", isOcuped: false },
        { name: "two", isOcuped: false },
        { name: "three", isOcuped: false },
        { name: "four", isOcuped: false },
        { name: "five", isOcuped: false }
      ],
      states: ["isDinnering", "isWaiting", "isSated"],
      x: 0,
      y: 0,
      flagFirstTurn: false
    };
  },

  methods: {
    initDinner() {
      (async () => {
        while (true) {
          await wait();
          this.getPartner();
        }
      })();

      async function wait() {
        return new Promise(resolve => {
          setTimeout(resolve, transitionTime);
        });
      }
    },

    dinnering(x, y) {
      for (let index = 0; index < this.philosophers.length; index++) {
        this.philosophers[index].state = 0;
        this.forks[this.philosophers[index].forks[0]].isOcuped = false;
        this.forks[this.philosophers[index].forks[1]].isOcuped = false;
      }
      this.philosophers[x].state = 1;
      this.forks[this.philosophers[x].forks[0]].isOcuped = true;
      this.forks[this.philosophers[x].forks[1]].isOcuped = true;
      this.philosophers[y].state = 1;
      this.forks[this.philosophers[y].forks[0]].isOcuped = true;
      this.forks[this.philosophers[y].forks[1]].isOcuped = true;

      if (this.flagFirstTurn) {
        if (x === 0) x = 4;
        else x--;
        if (y === 0) y = 4;
        else y--;
        this.philosophers[x].state = 2;
        this.philosophers[y].state = 2;
      }
      this.flagFirstTurn = true;
    },

    getPartner() {
      if (this.x > 2) this.y = this.x - 3;
      else this.y = this.x + 2;
      this.dinnering(this.x, this.y);
      this.x++;
      if (this.x === 5) this.x = 0;
    }
  }
};
</script>

<style lang="scss">
$table-roundedSize: 28em;
$table-roundedColor: gray;
$philosopherSize: 9em;
$philosopherColor: white;
$forkSize: 3em;
$forkColor: white;

$baseRotation: 72;

$mapPhilosophers: (socrates, platon, aristoteles, heraclito, pitagoras);

$mapNumbers: (one, two, three, four, five);

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  display: grid;
  grid-gap: 1em;
  justify-content: center;
}

.container {
  display: grid;
  grid-template-columns: 1fr 3fr;
  margin: 0 auto;
}
.state-indicator {
  display: flex;
  flex-direction: column;
  &__info:before {
    display: inline-block;
    color: inherit;
    border-radius: 50%;
    content: "";
    width: 1em;
    height: 1em;
    margin-right: 0.25em;
    border: 1px solid black;
  }
  &__info:nth-child(1) {
    &:before {
      background-color: green;
    }
  }
  &__info:nth-child(2) {
    &:before {
      background-color: orange;
    }
  }
  &__info:nth-child(3) {
    &:before {
      background-color: red;
    }
  }
}

.table-rounded {
  position: relative;
  background-color: $table-roundedColor;
  width: $table-roundedSize;
  height: $table-roundedSize;
  margin: auto;
  border-radius: 50%;
  justify-content: center;
  border: 1px solid black;
}

.container-fork {
  height: $table-roundedSize;
  width: $table-roundedSize;
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
  height: $table-roundedSize;
  width: $table-roundedSize;
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
    color: white;
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

.btn {
  justify-self: center;
  width: 50%;
  background-color: #00bfff;
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
