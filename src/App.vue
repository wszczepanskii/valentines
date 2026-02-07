<script setup>
import { ref, onMounted } from 'vue'
import confetti from 'canvas-confetti'

const audioUrl = `${import.meta.env.BASE_URL}/music/fukaj-kubi-producent-to-dla-ciebie_31DWq7e6.mp3`
const photos = [
  {
    url: `${import.meta.env.BASE_URL}img/img1.jpeg`,
    caption: 'Tak się zaczęło... 💕',
    date: '2022-06-01 (nie mam żadnego zdjecia z tego dnia, ale to pierwsze nasze wspólne zdjęcie, więc musi być na pierwszym miejscu!)',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img2.jpeg`,
    caption: 'Nasza wspólna pierwsza imprezka 🎉',
    date: '2022-10-29',
  },
  {
    url: `${import.meta.env.BASE_URL}img/wakacje.jpeg`,
    caption: 'Pierwsze wakacje razem! 🏖️',
    date: '2022-08-15',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img3.jpeg`,
    caption: 'Pierwsze walentynki razem! 🌹',
    date: '2023-02-14',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img4.jpeg`,
    caption: 'Croatia po raz drugi! 🇭🇷',
    date: '2023-07-27',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img5.jpeg`,
    caption: 'To jedzenie bylo takie pyszne',
    date: '2024-08-12',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img6.jpeg`,
    caption: 'Pierwszy raz na Cyprze! 🇨🇾',
    date: '2024-08-12',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img7.jpeg`,
    caption: 'Majóweczka z rodzicami 🌸',
    date: '2025-05-01',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img8.jpeg`,
    caption: 'Pierwsze all inclusive! 🍹',
    date: '2025-05-28',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img9.jpeg`,
    caption: 'City break w Berlinie 🏙️',
    date: '2025-09-06',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img10.jpeg`,
    caption: 'No i pyszny kebabik 🌯',
    date: '2022-09-07',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img12.jpeg`,
    caption: 'Najlepsze wakacje w Kenii! 🇰🇪',
    date: '2025-09-25',
  },
  {
    url: `${import.meta.env.BASE_URL}img/img11.jpeg`,
    caption: 'Safari 🐘',
    date: '2025-09-26',
  },
]

const currentPhotoIndex = ref(0)
let slideInterval = null

const audio = new Audio(audioUrl)
audio.loop = true // Zapętlenie muzyki
audio.volume = 0.05 // Głośność 50%

// Generowanie serduszek tła
const heartsCount = 40
const backgroundHearts = ref([])

const generateHeartStyle = () => {
  // Losowe parametry dla każdego serduszka
  const left = Math.random() * 100 // Pozycja pozioma 0-100%
  const size = Math.random() * 3 + 1 // Wielkość od 1rem do 4rem
  const duration = Math.random() * 15 + 10 // Czas lotu 10-25s
  const delay = Math.random() * -20 // Ujemne opóźnienie, żeby zaczęły w różnym momencie
  const opacity = Math.random() * 0.5 + 0.2 // Przeźroczystość

  return {
    left: `${left}%`,
    fontSize: `${size}rem`,
    animationDuration: `${duration}s`,
    animationDelay: `${delay}s`,
    opacity: opacity,
  }
}

onMounted(() => {
  // Tworzymy tablicę obiektów dla serduszek
  backgroundHearts.value = Array.from({ length: heartsCount }, (_, i) => ({
    id: i,
    style: generateHeartStyle(),
  }))
})

// --- LOGIKA STRONY ---
const step = ref('question') // 'question' lub 'accepted'
const noBtnStyle = ref({})

// Funkcja po kliknięciu TAK
const handleYes = () => {
  step.value = 'accepted'

  // Odtwarzanie muzyki
  audio.play().catch((e) => console.log('Blokada autoplay:', e))

  // Konfetti
  triggerConfetti()

  // 2. Start pokazu slajdów (zmienia zdjęcie co 3 sekundy)
  slideInterval = setInterval(() => {
    currentPhotoIndex.value = (currentPhotoIndex.value + 1) % photos.length
  }, 3000)
}

// Funkcja uciekającego przycisku NIE
const moveNoButton = (e) => {
  const btnWidth = 100
  const btnHeight = 50
  const margin = 20
  const maxWidth = window.innerWidth - btnWidth - margin
  const maxHeight = window.innerHeight - btnHeight - margin

  const newX = Math.max(margin, Math.random() * maxWidth)
  const newY = Math.max(margin, Math.random() * maxHeight)

  noBtnStyle.value = {
    position: 'fixed',
    left: `${newX}px`,
    top: `${newY}px`,
    zIndex: 9999,
    transition: 'all 0.2s ease',
  }
}

// Konfetti
const triggerConfetti = () => {
  const duration = 3000
  const animationEnd = Date.now() + duration
  const defaults = { startVelocity: 30, spread: 360, ticks: 60, zIndex: 10000 }

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
  <div class="background-hearts">
    <div v-for="heart in backgroundHearts" :key="heart.id" class="bg-heart" :style="heart.style">
      ❤️
    </div>
  </div>

  <div class="container main-content">
    <transition name="fade" mode="out-in">
      <div v-if="step === 'question'" key="question" class="card">
        <div class="heart-container">
          <div class="heart-main">❤️</div>
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
        <h1>No ja myślę!!!</h1>
        <p>Love you miss!! ❤️</p>

        <div class="slideshow-container">
          <transition-group name="fade-slide">
            <div
              v-for="(photo, index) in photos"
              :key="photo.url"
              v-show="index === currentPhotoIndex"
              class="slide"
            >
              <img :src="photo.url" class="slide-photo" />

              <div class="text-container">
                <div class="caption">{{ photo.caption }}</div>
                <div class="date">{{ photo.date }}</div>
              </div>
            </div>
          </transition-group>
        </div>

      </div>
    </transition>
  </div>
</template>

<style scoped>
/* NOWE: Style dla tła */
.background-hearts {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: -1; /* Tło musi być pod spodem */
  background: linear-gradient(to bottom, #fff5f7, #ffeef1); /* Delikatny różowy gradient */
  pointer-events: none; /* Żeby tło nie blokowało kliknięć */
}

.bg-heart {
  position: absolute;
  bottom: -10vh; /* Startują poza ekranem na dole */
  color: #ffc1cc; /* Jasny różowy kolor serc w tle */
  animation: floatUp linear infinite;
  text-shadow: 0 0 5px rgba(255, 182, 193, 0.5);
}

@keyframes floatUp {
  0% {
    transform: translateY(0) rotate(0deg);
  }
  100% {
    transform: translateY(-110vh) rotate(360deg); /* Lecą do góry i się kręcą */
  }
}

/* Reszta Twoich stylów */
.container.main-content {
  text-align: center;
  width: 100%;
  max-width: 600px;
  padding: 20px;
  position: relative; /* Ważne dla z-index */
  z-index: 1;
}

.card {
  background: rgba(255, 255, 255, 0.95); /* Lekko przeźroczyste tło karty */
  padding: 40px;
  border-radius: 20px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
  position: relative;
  backdrop-filter: blur(5px); /* Efekt rozmycia tła pod kartą */
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

/* Zmieniłem nazwę klasy na heart-main, żeby nie gryzła się z tłem */
.heart-main {
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

.buttons {
  display: flex;
  justify-content: center;
  gap: 20px;
  margin-top: 20px;
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

.slideshow-container {
  width: 400px; /* Zamiast sztywnego 300px, bierzemy 100% szerokości karty */
  height: 500px; /* Zwiększyliśmy wysokość z 400px na 500px (możesz dać nawet 600px) */
  margin: 30px auto; /* Trochę większy odstęp od góry i dołu */
  position: relative;
  background: white;
  padding: 15px 15px 100px 15px;
  box-sizing: border-box;
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.2); /* Większy cień dla efektu głębi */
  border-radius: 4px;
}


/* Upewnij się, że .slide-photo zostaje bez zmian, ale dla pewności wklejam: */
.slide-photo {
  width: 100%;
  height: 100%;
  object-fit: contain; /* cover = przycina, żeby wypełnić; contain = pokazuje całe zdjęcie z pasami */
  border-radius: 4px;
  position: absolute;
  top: 0;
  left: 0;
  padding: 10px; /* To musi pasować do paddingu kontenera */
  box-sizing: border-box;
}

.text-container {
  position: absolute;
  top: 100%; 
  left: 0;
  width: 100%;
  height: 90px;     /* Wysokość obszaru na tekst */
  display: flex;
  flex-direction: column; /* Układamy pionowo: podpis nad datą */
  align-items: center;
  justify-content: center;
}

.caption {
  font-family: 'Pacifico', cursive;
  font-size: 1.4rem;
  color: #333;
  margin-bottom: 5px;
  line-height: 1.2;
}

.date {
  font-family: 'Poppins', sans-serif; /* Prostsza czcionka dla daty */
  font-size: 0.9rem;
  color: #888; /* Szary kolor daty */
  font-weight: 500;
  letter-spacing: 1px;
}

/* Animacja przenikania zdjęć */
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: opacity 1s ease;
}

.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
}
</style>
