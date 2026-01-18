<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <div class="layout">
    <header class="header">
      <div class="brand">feel-allow-release</div>
      <nav class="nav">
        <RouterLink to="/" class="link" active-class="active">Home</RouterLink>
        <RouterLink to="/about" class="link" active-class="active">About</RouterLink>
      </nav>
    </header>

    <main class="main">
      <RouterView />
    </main>
  </div>
</template>

<style scoped>
.layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.header {
  position: sticky;
  top: 0;
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 16px;
  padding: 16px 20px;
  background: rgba(15, 23, 42, 0.9);
  backdrop-filter: blur(8px);
  border-bottom: 1px solid rgba(148, 163, 184, 0.2);
}

.brand {
  font-weight: 700;
  letter-spacing: 0.2px;
}

.nav {
  display: flex;
  gap: 12px;
}

.link {
  color: rgba(226, 232, 240, 0.85);
  text-decoration: none;
  padding: 6px 10px;
  border-radius: 10px;
}

.link:hover {
  background: rgba(148, 163, 184, 0.14);
}

.active {
  background: rgba(59, 130, 246, 0.25);
  color: rgba(226, 232, 240, 1);
}

.main {
  flex: 1;
  width: min(960px, 100%);
  margin: 0 auto;
  padding: 28px 20px;
}
</style>


