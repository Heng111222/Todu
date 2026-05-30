<script setup>
import { ref } from 'vue'

import confetti from 'canvas-confetti'

import spinSoundFile from '../assets/sounds/spin.mp3'
import winSoundFile from '../assets/sounds/win.mp3'

const spinSound = new Audio(spinSoundFile)
const winSound = new Audio(winSoundFile)

const rewards = [
  '$5',
  '$10',
  '$20',
  '$50',
  '$100',
  '$500'
]

const selectedReward = ref('')
const spinning = ref(false)
const currentIndex = ref(0)
const balance = ref(0)
const history = ref([])

function startSpin() {

  if (spinning.value) return

  spinning.value = true
  selectedReward.value = ''

  // បើកសំឡេងពេលបង្វិល
  spinSound.currentTime = 0
  spinSound.loop = true
  spinSound.play()

  let counter = 0

  const interval = setInterval(() => {

    currentIndex.value = Math.floor(
      Math.random() * rewards.length
    )

    counter++

    if (counter > 25) {

      clearInterval(interval)

      selectedReward.value =
        rewards[currentIndex.value]

      // បិទសំឡេងបង្វិល
      spinSound.pause()

      // បើកសំឡេងឈ្នះ
      winSound.currentTime = 0
      winSound.play()

      // បូកលុយចូលកាបូប
      balance.value += Number(
        selectedReward.value.replace('$', '')
      )

      // រក្សាទុកប្រវត្តិ
      history.value.push(
        selectedReward.value
      )

      // Effect ក្រដាសពណ៌
      confetti({
        particleCount: 200,
        spread: 100,
        origin: {
          y: 0.6
        }
      })

      spinning.value = false
    }

  }, 100)
}
</script>

<template>

  <div class="container">

    <div class="overlay"></div>

    <div class="game-card">

      <h1>
        💰 លេងហ្គេមបង្វិលយកលុយ
      </h1>

      <p>
        បង្វិលដើម្បីឈ្នះរង្វាន់លុយ
      </p>

      <!-- កាបូបលុយ -->
      <div class="wallet">
        កាបូបលុយ:
        <span>
          ${{ balance }}
        </span>
      </div>

      <!-- Spinner -->
      <div class="spinner-box">

        <div
          class="reward"
          :class="{
            active: index === currentIndex
          }"
          v-for="(reward, index) in rewards"
          :key="index"
        >
          {{ reward }}
        </div>

      </div>

      <!-- ប៊ូតុងបង្វិល -->
      <button
        @click="startSpin"
        :disabled="spinning"
      >
        {{
          spinning
            ? 'កំពុងបង្វិល...'
            : 'បង្វិលឥឡូវ'
        }}
      </button>

      <!-- លទ្ធផល -->
      <div
        v-if="selectedReward"
        class="result"
      >

        🎉 អ្នកឈ្នះ

        <span>
          {{ selectedReward }}
        </span>

      </div>

      <!-- ប្រវត្តិ -->
      <div
        v-if="history.length"
        class="history"
      >

        <h3>
          ប្រវត្តិរង្វាន់
        </h3>

        <ul>

          <li
            v-for="(item, index) in history"
            :key="index"
          >
            {{ item }}
          </li>

        </ul>

      </div>

    </div>

  </div>

</template>

<style scoped>

@import url('https://fonts.googleapis.com/css2?family=Kantumruy+Pro:wght@300;400;500;700&display=swap');

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

.container {

  min-height: 100vh;

  display: flex;

  justify-content: center;

  align-items: center;

  background:
    linear-gradient(
      135deg,
      #0f172a,
      #1e293b,
      #111827
    );

  overflow: hidden;

  position: relative;

  font-family:
    'Kantumruy Pro',
    sans-serif;
}

.overlay {

  position: absolute;

  width: 100%;

  height: 100%;

  background:
    radial-gradient(
      circle,
      rgba(255,255,255,0.08),
      transparent
    );
}

.game-card {

  width: 430px;

  background:
    rgba(255,255,255,0.08);

  border:
    1px solid rgba(255,255,255,0.15);

  backdrop-filter: blur(12px);

  border-radius: 25px;

  padding: 40px;

  text-align: center;

  color: white;

  z-index: 2;

  box-shadow:
    0 20px 50px rgba(0,0,0,0.4);
}

.game-card h1 {

  font-size: 38px;

  margin-bottom: 10px;
}

.game-card p {

  color: #cbd5e1;

  margin-bottom: 25px;
}

.wallet {

  background:
    rgba(255,255,255,0.08);

  padding: 15px;

  border-radius: 15px;

  margin-bottom: 25px;

  font-size: 20px;

  font-weight: bold;
}

.wallet span {
  color: #22c55e;
}

.spinner-box {

  display: grid;

  grid-template-columns:
    repeat(2, 1fr);

  gap: 15px;

  margin-bottom: 30px;
}

.reward {

  background:
    rgba(255,255,255,0.1);

  padding: 22px;

  border-radius: 15px;

  font-size: 28px;

  font-weight: bold;

  border: 2px solid transparent;

  transition: all 0.2s ease;
}

.reward:hover {
  transform: scale(1.05);
}

.reward.active {

  background: #22c55e;

  border-color: white;

  box-shadow:
    0 0 25px #22c55e;

  transform: scale(1.08);
}

button {

  width: 100%;

  padding: 16px;

  border: none;

  border-radius: 15px;

  background:
    linear-gradient(
      90deg,
      #22c55e,
      #16a34a
    );

  color: white;

  font-size: 18px;

  font-weight: bold;

  cursor: pointer;

  transition: 0.3s;

  font-family:
    'Kantumruy Pro',
    sans-serif;
}

button:hover {
  transform: translateY(-2px);
}

button:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.result {

  margin-top: 30px;

  font-size: 30px;

  font-weight: bold;
}

.result span {

  display: block;

  margin-top: 10px;

  font-size: 55px;

  color: #22c55e;
}

.history {

  margin-top: 35px;

  text-align: left;
}

.history h3 {
  margin-bottom: 15px;
}

.history ul {
  list-style: none;
}

.history li {

  background:
    rgba(255,255,255,0.08);

  padding: 12px;

  border-radius: 10px;

  margin-bottom: 10px;
}

</style>
