<template>
  <div class="app-container" :class="{ 'has-weather': weather }">
    <div class="weather-card" role="main" aria-live="polite" aria-label="Weather information">
      <form @submit.prevent="fetchWeather" class="search-form" aria-label="Search for city weather">
        <label for="city-input" class="visually-hidden">City name</label>
        <input
          id="city-input"
          type="text"
          v-model.trim="query"
          placeholder="Enter city name..."
          class="search-input"
          autocomplete="off"
          aria-required="true"
          aria-describedby="searchHelp"
        />
        <button type="submit" class="search-button" aria-label="Search weather">
          🔍
        </button>
        <p id="searchHelp" class="sr-only">Press enter or click search to get weather</p>
      </form>

      <transition name="fade" mode="out-in" appear>
        <div v-if="weather" key="weather-info" class="weather-info">
          <h2 class="location" tabindex="0">{{ weather.name }}, {{ weather.sys.country }}</h2>
          <p class="date">{{ formatDate(new Date()) }}</p>

          <div class="temp-wrapper" aria-label="Current temperature">
            <img
              :src="`https://openweathermap.org/img/wn/${weather.weather[0].icon}@4x.png`"
              :alt="weather.weather[0].description"
              class="weather-icon"
              width="100"
              height="100"
              loading="lazy"
              aria-hidden="true"
            />
            <p class="temp">{{ Math.round(weather.main.temp) }}°C</p>
          </div>

          <p class="description">{{ weather.weather[0].description }}</p>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: '',
      weather: null,
      apiKey: '129fa9f777ef71cc93123524f7f8e77e',
    };
  },
  methods: {
    async fetchWeather() {
      if (!this.query) return;

      try {
        const response = await fetch(
          `https://api.openweathermap.org/data/2.5/weather?q=${encodeURIComponent(this.query)}&units=metric&appid=${this.apiKey}`
        );
        if (!response.ok) throw new Error('City not found');
        this.weather = await response.json();
      } catch (err) {
        alert(err.message);
        this.weather = null;
      }
    },
    formatDate(d) {
      const days = ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'];
      const months = [
        'January', 'February', 'March', 'April', 'May', 'June',
        'July', 'August', 'September', 'October', 'November', 'December'
      ];
      return `${days[d.getDay()]}, ${d.getDate()} ${months[d.getMonth()]} ${d.getFullYear()}`;
    },
  },
};
</script>

<style scoped>
:root {
  --clr-bg-gradient-start: #1e3c72;
  --clr-bg-gradient-end: #2a5298;
  --clr-card-bg: rgba(255, 255, 255, 0.1);
  --clr-card-border: rgba(255, 255, 255, 0.25);
  --clr-text-primary: #e0e7ff;
  --clr-text-secondary: #a0aec0;
  --clr-accent: #60a5fa;
  --clr-input-bg: rgba(255, 255, 255, 0.15);
  --clr-input-placeholder: #cbd5e1;
  --clr-input-focus: #3b82f6;
  --font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  --border-radius: 16px;
  --transition-speed: 0.3s;
}

.app-container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 24px;
  background: linear-gradient(135deg, var(--clr-bg-gradient-start), var(--clr-bg-gradient-end));
  font-family: var(--font-family);
  color: var(--clr-text-primary);
  user-select: none;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  transition: background-color 0.5s ease;
}

.weather-card {
  background: var(--clr-card-bg);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-radius: var(--border-radius);
  border: 1px solid var(--clr-card-border);
  box-shadow:
    0 8px 24px rgba(0, 0, 0, 0.35),
    inset 0 0 1px 0 rgba(255, 255, 255, 0.1);
  width: 360px;
  max-width: 100%;
  padding: 32px 36px;
  text-align: center;
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.search-form {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
}

.search-input {
  flex: 1;
  padding: 12px 16px;
  border-radius: 12px;
  border: none;
  outline: none;
  font-size: 17px;
  font-weight: 500;
  background: var(--clr-input-bg);
  color: var(--clr-text-primary);
  transition: box-shadow var(--transition-speed), background var(--transition-speed);
  box-shadow: inset 0 0 6px rgba(255, 255, 255, 0.15);
}

.search-input::placeholder {
  color: var(--clr-input-placeholder);
  font-style: italic;
  user-select: none;
}

.search-input:focus {
  box-shadow: 0 0 8px var(--clr-input-focus);
  background: rgba(59, 130, 246, 0.15);
}

.search-button {
  background: var(--clr-accent);
  border: none;
  border-radius: 12px;
  padding: 12px 18px;
  color: white;
  font-size: 18px;
  cursor: pointer;
  user-select: none;
  transition: background-color var(--transition-speed), transform var(--transition-speed);
  box-shadow: 0 4px 12px rgba(96, 165, 250, 0.6);
}

.search-button:hover,
.search-button:focus {
  background-color: #2563eb;
  transform: scale(1.1);
  outline: none;
}

.weather-info {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  animation: fadeInUp 0.6s ease forwards;
}

.location {
  font-size: 26px;
  font-weight: 700;
  letter-spacing: 0.06em;
  text-shadow: 0 1px 6px rgba(0, 0, 0, 0.4);
}

.date {
  font-size: 14px;
  opacity: 0.7;
  letter-spacing: 0.03em;
  text-transform: uppercase;
  user-select: none;
}

.temp-wrapper {
  display: flex;
  align-items: center;
  gap: 16px;
}

.weather-icon {
  filter: drop-shadow(0 2px 2px rgba(0,0,0,0.25));
  flex-shrink: 0;
}

.temp {
  font-size: 58px;
  font-weight: 900;
  letter-spacing: -0.02em;
  user-select: none;
}

.description {
  font-size: 20px;
  font-weight: 600;
  text-transform: capitalize;
  color: var(--clr-text-secondary);
  user-select: none;
  font-style: italic;
}

.visually-hidden {
  position: absolute !important;
  height: 1px; width: 1px;
  overflow: hidden;
  clip: rect(1px, 1px, 1px, 1px);
  clip-path: inset(50%);
  white-space: nowrap;
  border: 0;
  padding: 0;
  margin: -1px;
}

.sr-only {
  position: absolute !important;
  height: 1px; width: 1px;
  overflow: hidden;
  clip: rect(1px, 1px, 1px, 1px);
  clip-path: inset(50%);
  white-space: nowrap;
  border: 0;
  padding: 0;
  margin: -1px;
}

/* Animations */

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.4s ease;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
}

@keyframes fadeInUp {
  0% {
    opacity: 0;
    transform: translateY(18px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Responsive */

@media (max-width: 400px) {
  .weather-card {
    padding: 24px 20px;
    width: 100%;
  }

  .temp {
    font-size: 48px;
  }

  .location {
    font-size: 20px;
  }
}
</style>
