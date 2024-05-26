<script setup>
import { ref } from 'vue'
import DiceComp from '../components/DiceComp.vue'

const current0 = ref(0)
const score0 = ref(0)
const current1 = ref(0)
const score1 = ref(0)
const diceNumber = ref(5)
const plr1turn = ref(true)
const plr0win = ref(false)
const plr1win = ref(false)

const diceHidden = ref(true)

const gameEnded = ref(false)

function newGame() {
  diceHidden.value = true
  current0.value = 0
  score0.value = 0
  current1.value = 0
  score1.value = 0
  plr0win.value = false
  plr1win.value = false
  gameEnded.value = false
}

async function awaitCallTimeout(fnc, sec) {
  await new Promise((resolve) => {
    setTimeout(() => fnc(), sec)
    resolve()
  })
}

async function roll() {
  if (gameEnded.value) return

  diceHidden.value = true

  awaitCallTimeout(() => rollInner(), 200)
  awaitCallTimeout(() => setRandomDiceNumber(), 350)
  awaitCallTimeout(() => setRandomDiceNumber(), 550)
  awaitCallTimeout(() => setRandomDiceNumber(), 750)
  awaitCallTimeout(() => setRandomDiceNumber(), 950)
  awaitCallTimeout(() => rollEnd(), 1150)
}

function setRandomDiceNumber() {
  const r = Math.floor(Math.random() * 6) + 1
  diceNumber.value = r
}

function rollInner() {
  diceHidden.value = false

  console.log('roll')
}

function rollEnd() {
  if (diceNumber.value == 1) {
    endTurn()
  } else {
    if (plr1turn.value) {
      current0.value += diceNumber.value
    } else {
      current1.value += diceNumber.value
    }
  }
}

function endTurn() {
  if (plr1turn.value) {
    current0.value = 0
  } else {
    current1.value = 0
  }
  plr1turn.value = !plr1turn.value
  diceHidden.value = true
}

function endGame() {
  diceHidden.value = true

  gameEnded.value = true
  var audio = new Audio('./src/assets/413203__joepayne__clean-and-pompous-fanfare-trumpet.mp3')
  audio.play()
}

function hold() {
  if (gameEnded.value) return

  if (plr1turn.value) {
    score0.value += current0.value
    if (score0.value >= 100) {
      plr0win.value = true
      endGame()
    }
  } else {
    score1.value += current1.value
    if (score1.value >= 100) {
      plr1win.value = true
      endGame()
    }
  }
  endTurn()
}
</script>

<template>
  <main :class="{ pyro: gameEnded }">
    <div class="before"></div>
    <section
      class="player--0"
      :class="{ player: true, playeractive: plr1turn, playerwinner: plr0win }"
    >
      <h2 class="name" id="name--0">Player 1</h2>
      <p class="score">{{ score0 }}</p>
      <div class="current">
        <p class="current-label">Current</p>
        <p class="current-score">{{ current0 }}</p>
      </div>
    </section>
    <section class="player player--1" :class="{ playeractive: !plr1turn, playerwinner: plr1win }">
      <h2 class="name" id="name--1">Player 2</h2>
      <p class="score">{{ score1 }}</p>
      <div class="current">
        <p class="current-label">Current</p>
        <p class="current-score">{{ current1 }}</p>
      </div>
    </section>

    <DiceComp :diceNumber="diceNumber" :hidden="diceHidden" />
    <button class="btn btn--new" @click="newGame">🔄 New game</button>
    <button class="btn btn--roll" @click="roll">🎲 Roll dice</button>
    <button class="btn btn--hold" @click="hold">📥 Hold</button>
    <div class="after"></div>
  </main>
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Nunito&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: inherit;
}

html {
  font-size: 62.5%;
  box-sizing: border-box;
}

body {
  font-family: 'Nunito', sans-serif;
  font-weight: 400;
  height: 100vh;
  color: #333;
  background-image: linear-gradient(to top left, #753682 0%, #bf2e34 100%);
  display: flex;
  align-items: center;
  justify-content: center;
}

/* LAYOUT */
main {
  position: relative;
  width: 100rem;
  height: 60rem;
  background-color: rgba(255, 255, 255, 0.35);
  backdrop-filter: blur(200px);
  filter: blur();
  box-shadow: 0 3rem 5rem rgba(0, 0, 0, 0.25);
  border-radius: 9px;
  overflow: hidden;
  display: flex;
}

.player {
  flex: 50%;
  padding: 3rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  transition: all 0.75s;
}

/* ELEMENTS */
.name {
  position: relative;
  font-size: 4rem;
  text-transform: uppercase;
  letter-spacing: 1px;
  word-spacing: 2px;
  font-weight: 300;
  margin-bottom: 1rem;
}

.score {
  font-size: 8rem;
  font-weight: 300;
  color: #c7365f;
  margin-bottom: 40;
}

.playeractive {
  background-color: rgba(255, 255, 255, 0.4);
}
.playeractive .name {
  font-weight: 700;
}
.playeractive .score {
  font-weight: 400;
}

.playeractive .current {
  opacity: 1;
}

.current {
  background-color: #c7365f;
  opacity: 0.8;
  border-radius: 9px;
  color: #fff;
  width: 65%;
  padding: 2rem;
  text-align: center;
  transition: all 0.75s;
}

.current-label {
  text-transform: uppercase;
  margin-bottom: 1rem;
  font-size: 1.7rem;
  color: #ddd;
}

.current-score {
  font-size: 3.5rem;
}

/* ABSOLUTE POSITIONED ELEMENTS */
.btn {
  position: absolute;
  left: 50%;
  transform: translateX(-50%);
  color: #444;
  background: none;
  border: none;
  font-family: inherit;
  font-size: 1rem;
  text-transform: uppercase;
  cursor: pointer;
  font-weight: 400;
  transition: all 0.2s;

  background-color: white;
  background-color: rgba(255, 255, 255, 0.6);
  backdrop-filter: blur(10px);

  padding: 0.7rem 2rem;
  border-radius: 50rem;
  box-shadow: 0 1.75rem 3.5rem rgba(0, 0, 0, 0.1);
}

.btn::first-letter {
  font-size: 2.4rem;
  display: inline-block;
  margin-right: 0.7rem;
}

.btn--new {
  top: 4rem;
}
.btn--roll {
  top: 20rem;
}
.btn--hold {
  top: 25rem;
}

.btn:active {
  transform: translate(-50%, 3px);
  box-shadow: 0 1rem 2rem rgba(0, 0, 0, 0.15);
}

.btn:focus {
  outline: none;
}

img.dice {
  position: absolute;
  left: 50%;
  top: 10rem;
  transform: translateX(-50%);
  height: 10rem;
  box-shadow: 0 2rem 5rem rgba(0, 0, 0, 0.2);
  animation: spin 1s linear 1;
}

@keyframes spin {
  20% {
    transform: translateX(-50%) rotate(45deg);
  }
  40% {
    transform: translateX(-50%) rotate(135deg);
  }
  60% {
    transform: translateX(-50%) rotate(225deg);
  }
  80% {
    transform: translateX(-50%) rotate(315deg);
  }
  100% {
    transform: translateX(-50%) rotate(360deg);
  }
}

.playerwinner {
  background-color: #2f2f2f;
}

.playerwinner .name {
  font-weight: 700;
  color: #c7365f;
}

.hidden {
  display: none;
}
</style>
