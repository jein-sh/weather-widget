<script setup>
import { ref, onMounted, watch, computed } from 'vue'
import dayjs from 'dayjs'

const apiKey = 'f69b403e7211f2437b887c54cd8ca0c6'
const unsplashAccessKey = '_XN9ruDhZmAgMXeDfWp8FR7jcssmGy1v6aH8uLKry7Y'
const weather = ref(null)
const backgroundImage = ref('')
const currentDate = ref(dayjs().format('dddd, D MMMM'))

const fetchWeather = async (lat, lon) => {
  try {
    const res = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&units=metric&lang=en&appid=${apiKey}`
    )
    const data = await res.json()
    weather.value = data
  } catch (error) {
    console.error('Ошибка запроса:', error)
  }
}

const fetchCityImage = async (cityName) => {
  try {
    const res = await fetch(
      `https://api.unsplash.com/search/photos?query=${cityName}&orientation=portrait&client_id=${unsplashAccessKey}`
    )
    const data = await res.json()
    backgroundImage.value = data.results[0]?.urls?.regular || ''
  } catch (error) {
    console.error('Ошибка загрузки изображения:', error)
  }
}

const iconSrc = computed(() => {
  if (!weather.value) return ''
  const iconCode = weather.value.weather[0].icon 
  return new URL(`/src/assets/weather-icons/${iconCode}.svg`, import.meta.url).href
})

const getGeolocation = () => {
  if (!navigator.geolocation) {
    error.value = 'Геолокация не поддерживается вашим браузером'
    return
  }

  navigator.geolocation.getCurrentPosition(
    position => {
      const { latitude, longitude } = position.coords
      fetchWeather(latitude, longitude)
    },
    err => {
      error.value = 'Ошибка доступа к геолокации: ' + err.message
      console.error(err)
    }
  )
}

const hueValue = computed(() => {
  if (!weather.value?.main?.temp) return 0
  const temp = Math.max(-30, Math.min(30, weather.value.main.temp))
  if (temp <= 0) {
    // От -30 до 0: фиолетовый → голубой
    const ratio = (temp + 30) / 30
    return 270 - ratio * 90 
  } else {
    // От 0 до 30: жёлтый → красный
    const ratio = temp / 30
    return 60 - ratio * 60 
  }
})

watch(
  () => weather.value?.name,
  (cityName) => {
    if (cityName) fetchCityImage(cityName)
  }
)

onMounted(() => {
  getGeolocation()
})
</script>

<template>
<div class="widget" v-if="weather">
  <div class="widget__img" :style="{ backgroundImage: `url(${backgroundImage})` }"></div>
  <div class="widget__overlay" :style="{ filter: `hue-rotate(${hueValue}deg)` }"></div>
  <div class="widget__top">
    <div class="widget__degrees">{{ Math.round(weather.main.temp) }}&deg;</div>
    <div class="widget__place"> {{ weather.name }}</div>
  </div>
  <div class="widget__bottom">
    <div class="widget__date">{{ currentDate }}</div>
    <div class="widget__weather">{{ weather.weather[0].description }}</div>
    <div class="widget__minmax"> {{ Math.round(weather.main.temp_min) }}&deg; /  {{ Math.round(weather.main.temp_max) }}&deg;</div>
    <div class="widget__icon">
        <img
          :src="iconSrc"
          alt="icon"
        />
    </div>
  </div>
</div>
</template>

<style scoped>
.widget {
  position: relative;
  display: flex;
  flex-direction: column;
  width: 243px;
  height: 332px;

  box-shadow: 0px 8px 16px rgba(0, 0, 0, 0.25);
  border-radius: 16px;
  overflow: hidden;
}

.widget__img {
  position: absolute;
  width: 100%;
  height: 100%;
  z-index: 1;
  background-size: cover;
}

.widget__overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background: linear-gradient(322.09deg, rgba(249, 57, 57, 0.2) 13.99%, rgba(255, 98, 98, 0.2) 104.25%), 
    linear-gradient(273.21deg, #F94545 -7%, rgba(255, 80, 80, 0.65) 102.49%);
    z-index: 2;
}

.widget__top {
  position: relative;
  padding: 24px;
  color: #fff;
  z-index: 3;
}

.widget__degrees {
  font-weight: 600;
  font-size: 40px;
  line-height: 54px;
  letter-spacing: -0.02em;
}

.widget__place {
  font-weight: 700;
  font-size: 15px;
  line-height: 20px;
  letter-spacing: 1px;
  text-transform: uppercase;
}

.widget__bottom {
  position: relative;
  margin-top: auto;
  background: #FFF;
  color: #191919;
  padding: 16px;
  z-index: 3;
  text-transform: capitalize;
}

.widget__date {
  font-weight: 700;
  font-size: 15px;
  line-height: 20px;
  opacity: 0.7;
  margin-bottom: 6px;
}

.widget__weather {
  font-weight: 700;
  font-size: 12px;
  line-height: 16px;
  color: #6B6B6B;
  opacity: 0.4;
  margin-bottom: 2px;
  padding-right: 40px;
}

.widget__minmax {
  font-weight: 700;
  font-size: 12px;
  line-height: 16px;
  color: #6B6B6B;
  opacity: 0.4;
  padding-right: 40px;
}

.widget__icon {
  position: absolute;
  bottom: 23px;
  right: 20px;
  width: 30px;
  height: 30px;
}
</style>
