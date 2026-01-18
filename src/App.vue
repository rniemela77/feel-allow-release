<script setup>
import { computed, ref } from 'vue'
import feel from './data/feel.json'

const selectedAnswerId = ref(null)

const question = computed(() => feel.question)
const answers = computed(() => feel.answers ?? [])

const selectedAnswer = computed(() => {
  if (!selectedAnswerId.value) return null
  return answers.value.find((a) => a.id === selectedAnswerId.value) ?? null
})

function selectAnswer(answerId) {
  selectedAnswerId.value = answerId
}

function reset() {
  selectedAnswerId.value = null
}
</script>

<template>
  <main class="page">
    <section class="card" v-if="!selectedAnswer">
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

    <section class="card" v-else>
      <div class="brand">feel · allow · release</div>
      <h1 class="title">{{ selectedAnswer.label }}</h1>

      <ol class="tips">
        <li v-for="(tip, idx) in (selectedAnswer.tips ?? []).slice(0, 3)" :key="idx" class="tip">
          {{ tip }}
        </li>
      </ol>

      <button type="button" class="resetBtn" @click="reset()">Start over</button>
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

.resetBtn {
  margin-top: 18px;
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
</style>
