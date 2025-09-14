<script setup>
import { RouterLink, RouterView } from 'vue-router'
</script>

<template>
  <div class="app-container">
    <!-- Encabezado fijo -->
    <header class="header">
      <h1 class="titulo animate-title">Generador de números pseudoaleatorios</h1>
      <nav class="nav">
        <RouterLink to="/" class="link">Algoritmo lineal</RouterLink>
        <RouterLink to="/about" class="link">Algoritmo multiplicativo</RouterLink>
      </nav>
    </header>

    <!-- Contenido principal ocupa toda la pantalla -->
    <main class="contenido">
      <div class="card animate-card">
        <RouterView />
      </div>
    </main>
  </div>
</template>

<style scoped>
.app-container {
  background-color: #ffffff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  font-family: 'Caviar Dreams', sans-serif;
  color: #333;
  overflow-x: hidden;
}

/* Header fijo arriba */
.header {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  background: #fff;
  border-bottom: 2px solid #f0f0f0;
  padding: 1rem 0;
  text-align: center;
  z-index: 10;
  box-shadow: 0 2px 10px rgba(0,0,0,0.05);
}

.titulo {
  font-size: 2rem;
  font-weight: bold;
  color: #b30000;
  margin-bottom: 0.5rem;
  letter-spacing: 1px;
  transition: transform 0.3s ease;
}

.titulo:hover {
  transform: scale(1.05);
}

/* Animación de título */
.animate-title {
  opacity: 0;
  transform: translateY(-20px) scale(0.95);
  animation: fadeSlide 0.8s ease forwards;
}

@keyframes fadeSlide {
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
  }
}

/* Navegación */
.nav {
  display: flex;
  justify-content: center;
  gap: 2rem;
}

.link {
  position: relative;
  text-decoration: none;
  font-size: 1rem;
  color: #cc0000;
  padding: 0.5rem 1rem;
  border-radius: 8px;
  transition: color 0.3s ease;
}

.link::after {
  content: "";
  position: absolute;
  left: 0;
  bottom: -4px;
  width: 0%;
  height: 2px;
  background: #cc0000;
  transition: width 0.3s ease;
}

.link:hover::after {
  width: 100%;
}

.router-link-exact-active {
  background-color: #cc0000;
  color: #fff !important;
  font-weight: bold;
  box-shadow: 0 3px 8px rgba(204,0,0,0.3);
}

/* Contenido ocupa el resto de la pantalla */
.contenido {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 6rem 1rem 2rem; /* espacio para que no tape el header */
  box-sizing: border-box;
}

/* Estilo de tarjetas internas */
.card {
  background: #fff;
  border: 1px solid #f0f0f0;
  border-radius: 16px;
  padding: 2.5rem;
  box-shadow: 0 6px 18px rgba(0,0,0,0.08);
  max-width: 700px;
  width: 100%;
  text-align: center;
  transition: all 0.4s ease;
}

.card:hover {
  transform: translateY(-6px);
  box-shadow: 0 8px 25px rgba(204,0,0,0.25);
}

/* Animación de la tarjeta */
.animate-card {
  opacity: 0;
  transform: translateY(30px);
  animation: fadeUp 0.9s ease forwards 0.2s;
}

@keyframes fadeUp {
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
