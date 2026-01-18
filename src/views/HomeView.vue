<script setup>
import { storeToRefs } from 'pinia'
import { useCounterStore } from '@/stores/counter'

const counter = useCounterStore()
const { count, doubleCount } = storeToRefs(counter)
</script>

<template>
  <section class="card">
    <h1>Home</h1>
    <p class="muted">
      This is a tiny Vue 3 app wired with <strong>Pinia</strong> and <strong>Vue Router</strong>.
    </p>

    <div class="statRow">
      <div class="stat">
        <div class="label">count</div>
        <div class="value">{{ count }}</div>
      </div>
      <div class="stat">
        <div class="label">doubleCount</div>
        <div class="value">{{ doubleCount }}</div>
      </div>
    </div>

    <div class="actions">
      <button class="btn primary" type="button" @click="counter.increment()">Increment</button>
      <button class="btn" type="button" @click="counter.reset()">Reset</button>
    </div>
  </section>
</template>

<style scoped>
.card {
  padding: 22px;
  border-radius: 18px;
  border: 1px solid rgba(148, 163, 184, 0.2);
  background: rgba(2, 6, 23, 0.6);
}

.muted {
  color: rgba(226, 232, 240, 0.75);
}

.statRow {
  margin-top: 18px;
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 12px;
}

.stat {
  padding: 14px 16px;
  border-radius: 14px;
  background: rgba(148, 163, 184, 0.08);
  border: 1px solid rgba(148, 163, 184, 0.15);
}

.label {
  font-size: 12px;
  color: rgba(226, 232, 240, 0.7);
  text-transform: uppercase;
  letter-spacing: 0.08em;
}

.value {
  margin-top: 6px;
  font-size: 28px;
  font-weight: 700;
}

.actions {
  margin-top: 18px;
  display: flex;
  gap: 10px;
}

.btn {
  cursor: pointer;
  border-radius: 12px;
  padding: 10px 14px;
  border: 1px solid rgba(148, 163, 184, 0.2);
  background: rgba(148, 163, 184, 0.12);
  color: rgba(226, 232, 240, 0.95);
}

.btn:hover {
  background: rgba(148, 163, 184, 0.18);
}

.primary {
  border-color: rgba(59, 130, 246, 0.35);
  background: rgba(59, 130, 246, 0.25);
}

.primary:hover {
  background: rgba(59, 130, 246, 0.32);
}
</style>


