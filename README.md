# 🌤️ Weather Widget

Интерактивный виджет прогноза погоды с определением местоположения и динамическим фоном города через Unsplash. Создан на Vue 3.

---

## 🚀 Возможности

- Получение геолокации пользователя (через `navigator.geolocation`)
- Загрузка текущей погоды с OpenWeather API
- Отображение:
  - Температуры, города
  - Минимальной/максимальной температуры
  - Описания погоды
  - Текущей даты (с `dayjs`)
- Динамическое фоновое изображение города через Unsplash API

---

## 🛠️ Технологии

- [Vue 3 (Composition API)](https://vuejs.org/)
- [OpenWeather API](https://openweathermap.org/current)
- [Unsplash API](https://unsplash.com/developers)
- [Day.js](https://day.js.org/) для форматирования даты

---

## Установка

npm install

## Запуск проекта для разработки

npm run dev

## Сборка для продакшена

npm run build