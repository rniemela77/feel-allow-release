<script setup>
import { computed, onBeforeUnmount, ref, watch } from 'vue'
import feel from './data/feel.json'

const step = ref('question') // 'question' | 'tips' | 'allow' | 'followup' | 'complete'
const selectedAnswerId = ref(null)
const followupResponse = ref(null) // 'same' | 'less' | 'more' | 'different' | null

const question = computed(() => feel.question)
const answers = computed(() => feel.answers ?? [])

const selectedAnswer = computed(() => {
  if (!selectedAnswerId.value) return null
  return answers.value.find((a) => a.id === selectedAnswerId.value) ?? null
})

function selectAnswer(answerId) {
  selectedAnswerId.value = answerId
  step.value = 'tips'
}

function reset() {
  selectedAnswerId.value = null
  followupResponse.value = null
  step.value = 'question'
}

function goToTips() {
  if (!selectedAnswer.value) return
  step.value = 'tips'
}

function goToAllow() {
  if (!selectedAnswer.value) return
  step.value = 'allow'
}

function goToFollowup() {
  if (!selectedAnswer.value) return
  step.value = 'followup'
}

function answerFollowup(value) {
  followupResponse.value = value

  if (value === 'different') {
    reset()
    return
  }

  step.value = 'complete'
}

// --- 60s timer (simple)
const secondsLeft = ref(60)
let intervalId = null

function stopTimer() {
  if (intervalId) {
    clearInterval(intervalId)
    intervalId = null
  }
}

function startTimer() {
  stopTimer()
  secondsLeft.value = 60
  intervalId = setInterval(() => {
    secondsLeft.value = Math.max(0, secondsLeft.value - 1)
    if (secondsLeft.value === 0) {
      stopTimer()
      // After 60 seconds, move to the follow-up automatically.
      goToFollowup()
    }
  }, 1000)
}

const timerLabel = computed(() => {
  const s = secondsLeft.value
  const mm = String(Math.floor(s / 60)).padStart(2, '0')
  const ss = String(s % 60).padStart(2, '0')
  return `${mm}:${ss}`
})

watch(
  step,
  (next) => {
    if (next === 'allow') startTimer()
    else stopTimer()
  },
  { immediate: true }
)

onBeforeUnmount(() => stopTimer())
</script>

<template>
  <main class="page">
    <section class="card" v-if="step === 'question'">
      <div class="brand">feel · allow · release</div>
      <h1 class="title">{{ question.text }}</h1>
      <p v-if="question.instruction" class="instruction">
        {{ question.instruction }}
      </p>

      <div class="answers" role="list">
        <button
          v-for="a in answers"
          :key="a.id"
          type="button"
          class="answerBtn"
          @click="selectAnswer(a.id)"
        >
          {{ a.label }}
        </button>
      </div>
    </section>

    <section class="card" v-else-if="step === 'tips'">
      <div class="brand">feel · allow · release</div>
      <h1 class="title">{{ selectedAnswer.label }}</h1>

      <ol class="tips">
        <li v-for="(tip, idx) in (selectedAnswer.tips ?? []).slice(0, 3)" :key="idx" class="tip">
          {{ tip }}
        </li>
      </ol>

      <div class="actions">
        <button type="button" class="primaryBtn" @click="goToAllow()">Allow for 60 seconds</button>
        <button type="button" class="resetBtn" @click="reset()">Start over</button>
      </div>
    </section>

    <section class="card" v-else-if="step === 'allow'">
      <div class="brand">chosen feeling</div>
      <div class="chosenFeeling">{{ selectedAnswer.label }}</div>

      <p class="instruction allowInstruction">Let the feeling be here. Don’t resist it.</p>

      <div class="timer" aria-live="polite">
        <div class="timerLabel">Time</div>
        <div class="timerValue">{{ timerLabel }}</div>
      </div>

      <div class="actions">
        <button type="button" class="primaryBtn" @click="goToFollowup()">Done</button>
        <button type="button" class="resetBtn" @click="goToTips()">Back</button>
      </div>
    </section>

    <section class="card" v-else-if="step === 'followup'">
      <div class="brand">one question</div>
      <h1 class="title">What’s true now?</h1>

      <div class="answers" role="list">
        <button type="button" class="answerBtn" @click="answerFollowup('same')">Same</button>
        <button type="button" class="answerBtn" @click="answerFollowup('less')">Less intense</button>
        <button type="button" class="answerBtn" @click="answerFollowup('more')">More intense</button>
        <button type="button" class="answerBtn" @click="answerFollowup('different')">
          Different feeling
        </button>
      </div>
    </section>

    <section class="card" v-else>
      <div class="brand">done</div>
      <h1 class="title">Thanks.</h1>
      <p class="instruction" v-if="followupResponse">
        You said: <span class="pill">{{ followupResponse }}</span>
      </p>
      <div class="actions">
        <button type="button" class="primaryBtn" @click="reset()">Start over</button>
      </div>
    </section>
  </main>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: grid;
  place-items: center;
  padding: 28px 20px;
}

.card {
  width: min(720px, 100%);
  padding: 22px;
  border-radius: 18px;
  border: 1px solid rgba(148, 163, 184, 0.2);
  background: rgba(2, 6, 23, 0.6);
}

.brand {
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.14em;
  color: rgba(226, 232, 240, 0.7);
}

.title {
  margin-top: 10px;
}

.instruction {
  margin-top: 10px;
  color: rgba(226, 232, 240, 0.78);
}

.allowInstruction {
  margin-top: 14px;
}

.answers {
  margin-top: 18px;
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 12px;
}

@media (max-width: 520px) {
  .answers {
    grid-template-columns: 1fr;
  }
}

.answerBtn {
  cursor: pointer;
  text-align: left;
  border-radius: 14px;
  padding: 14px 16px;
  border: 1px solid rgba(148, 163, 184, 0.2);
  background: rgba(148, 163, 184, 0.12);
  color: rgba(226, 232, 240, 0.95);
}

.answerBtn:hover {
  background: rgba(148, 163, 184, 0.18);
}

.answerBtn:focus-visible {
  outline: 2px solid rgba(59, 130, 246, 0.6);
  outline-offset: 2px;
}

.tips {
  margin: 16px 0 0 0;
  padding-left: 18px;
}

.tip {
  margin-top: 10px;
  color: rgba(226, 232, 240, 0.92);
}

.actions {
  margin-top: 18px;
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.primaryBtn {
  cursor: pointer;
  border-radius: 12px;
  padding: 10px 14px;
  border: 1px solid rgba(59, 130, 246, 0.35);
  background: rgba(59, 130, 246, 0.25);
  color: rgba(226, 232, 240, 0.95);
}

.primaryBtn:hover {
  background: rgba(59, 130, 246, 0.32);
}

.resetBtn {
  cursor: pointer;
  border-radius: 12px;
  padding: 10px 14px;
  border: 1px solid rgba(148, 163, 184, 0.2);
  background: rgba(148, 163, 184, 0.1);
  color: rgba(226, 232, 240, 0.85);
}

.resetBtn:hover {
  background: rgba(148, 163, 184, 0.16);
}

.chosenFeeling {
  margin-top: 10px;
  font-size: 22px;
  font-weight: 700;
}

.timer {
  margin-top: 16px;
  padding: 14px 16px;
  border-radius: 14px;
  background: rgba(148, 163, 184, 0.08);
  border: 1px solid rgba(148, 163, 184, 0.15);
}

.timerLabel {
  font-size: 12px;
  color: rgba(226, 232, 240, 0.7);
  text-transform: uppercase;
  letter-spacing: 0.08em;
}

.timerValue {
  margin-top: 6px;
  font-size: 30px;
  font-weight: 800;
}

.pill {
  display: inline-block;
  padding: 2px 8px;
  border-radius: 999px;
  border: 1px solid rgba(148, 163, 184, 0.18);
  background: rgba(148, 163, 184, 0.12);
  color: rgba(226, 232, 240, 0.9);
}
</style>
