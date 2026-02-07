<script setup>
import { ref, nextTick } from 'vue'
import confetti from 'canvas-confetti'

// Zmienne stanu
const step = ref('question') // 'question' lub 'accepted'
const noBtnStyle = ref({}) // Styl dla uciekającego przycisku

// Funkcja po kliknięciu TAK
const handleYes = () => {
  step.value = 'accepted'
  triggerConfetti()
}

// Funkcja uciekającego przycisku NIE
// Funkcja uciekającego przycisku NIE
const moveNoButton = (e) => {
  // Pobieramy wymiary przycisku (lub zakładamy przybliżone, np. 100x50)
  const btnWidth = 100
  const btnHeight = 50

  // Margines, żeby przycisk nie przyklejał się do samej krawędzi
  const margin = 20

  // Obliczamy maksymalne X i Y, gdzie przycisk może trafić
  // window.innerWidth to szerokość ekranu
  const maxWidth = window.innerWidth - btnWidth - margin
  const maxHeight = window.innerHeight - btnHeight - margin

  // Losujemy nową pozycję
  // Math.max(margin, ...) zapewnia, że nie ucieknie w lewo/górę
  const newX = Math.max(margin, Math.random() * maxWidth)
  const newY = Math.max(margin, Math.random() * maxHeight)

  noBtnStyle.value = {
    position: 'fixed', // ZMIANA: fixed odnosi się do okna przeglądarki, a nie rodzica
    left: `${newX}px`,
    top: `${newY}px`,
    zIndex: 9999, // Żeby był zawsze na wierzchu
    transition: 'all 0.2s ease', // Płynność ruchu
  }
}

// Konfetti
const triggerConfetti = () => {
  const duration = 3000
  const animationEnd = Date.now() + duration
  const defaults = { startVelocity: 30, spread: 360, ticks: 60, zIndex: 0 }

  const random = (min, max) => Math.random() * (max - min) + min

  const interval = setInterval(function () {
    const timeLeft = animationEnd - Date.now()

    if (timeLeft <= 0) {
      return clearInterval(interval)
    }

    const particleCount = 50 * (timeLeft / duration)

    confetti({
      ...defaults,
      particleCount,
      origin: { x: random(0.1, 0.3), y: Math.random() - 0.2 },
    })
    confetti({
      ...defaults,
      particleCount,
      origin: { x: random(0.7, 0.9), y: Math.random() - 0.2 },
    })
  }, 250)
}
</script>

<template>
  <div class="container">
    <transition name="fade" mode="out-in">
      <div v-if="step === 'question'" key="question" class="card">
        <div class="heart-container">
          <div class="heart">❤️</div>
        </div>
        <h1>Misiaczku...</h1>
        <p>Czy zostaniesz moją Walentynką?</p>

        <div class="buttons">
          <button @click="handleYes" class="btn yes-btn">Tak, oczywiście! 💖</button>

          <button
            @mouseover="moveNoButton"
            @click.prevent="moveNoButton"
            @touchstart.prevent="moveNoButton"
            :style="noBtnStyle"
            class="btn no-btn"
          >
            Nie 😢
          </button>
        </div>
      </div>

      <div v-else key="accepted" class="card accepted">
        <div class="bears">🐻🥰🐱</div>
        <h1>Wiedziałem!</h1>
        <p>Nie mogę się doczekać 14 lutego! Kocham Cię! ❤️</p>
        <button @click="triggerConfetti" class="btn celebrate-btn">Jeszcze raz konfetti! 🎉</button>
      </div>
    </transition>
  </div>
</template>

<style scoped>
/* Kontener główny */
.container {
  text-align: center;
  width: 100%;
  max-width: 600px;
  padding: 20px;
}

/* Karta */
.card {
  background: white;
  padding: 40px;
  border-radius: 20px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
  position: relative;
}

h1 {
  font-family: 'Pacifico', cursive;
  color: #d32f2f;
  font-size: 3rem;
  margin-bottom: 10px;
}

p {
  font-size: 1.2rem;
  color: #555;
  margin-bottom: 30px;
}

/* Animowane serce */
.heart {
  font-size: 5rem;
  animation: heartbeat 1.5s infinite;
  display: inline-block;
  margin-bottom: 20px;
}

@keyframes heartbeat {
  0% {
    transform: scale(1);
  }
  25% {
    transform: scale(1.1);
  }
  40% {
    transform: scale(1);
  }
  60% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}

/* Przyciski */
.buttons {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-top: 20px;
  /* Ważne dla pozycjonowania uciekającego przycisku */
  position: static;
}

.btn {
  padding: 12px 24px;
  font-size: 1.1rem;
  border: none;
  border-radius: 50px;
  cursor: pointer;
  transition:
    transform 0.2s,
    box-shadow 0.2s;
  font-family: 'Poppins', sans-serif;
  font-weight: 600;
}

.btn:hover {
  transform: scale(1.05);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.yes-btn {
  background: linear-gradient(45deg, #ff416c, #ff4b2b);
  color: white;
}

.no-btn {
  background: #e0e0e0;
  color: #333;
}

.celebrate-btn {
  background: #4caf50;
  color: white;
  margin-top: 20px;
}

/* Animacja przejścia między widokami */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.bears {
  font-size: 4rem;
  margin-bottom: 10px;
}
</style>
