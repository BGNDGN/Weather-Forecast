<template>
  <div id="baslangic">
    <div id="input"> 
      <h2 id="baslik">Weather Application</h2>
      <input id="value" type="text" placeholder="Bir şehir ismi girin." v-model="city">
      <button id="button" @click="appendValue">Search</button>
    </div>

    <div id="Main" v-if="weather">
      <p class="a">{{ weather.name }} - {{ weather.country }}</p>
      <div id="m1">
        <p class="a2">{{ weather.temperature }}°C</p>
      </div>
      <hr class="cizgi">
      <p class="hidden">ABC</p>
      <div v-for="hava in havadurumlari" :key="hava.id" class="mainn">
        <p class="a3">{{ weather.description }}</p>
        <img class="image" :src="require(`@/assets/${hava.image}`)" :alt="hava.name"/>
      </div>
    </div>

    <div id="forecast" v-if="forecast.length">
      <h3>4 Günlük Tahmin</h3>
      <div id="abc">
      <div class="inanılmaz">Gün </div>
      <div class="inanılmaz2">Sıcaklık</div>
      <div class="inanılmaz3">Açıklama</div>
      <div class="inanılmaz4">Durum</div>
      </div>
      <div v-for="day in forecast" :key="day.date" class="forecast-item">
        <div class="b1"><p>{{ day.date }}</p></div>
        <div class="b2"><p>{{ day.temperature }}°C</p></div>
        <div class="b3"><p>{{ day.description }}</p></div>
        <div class="b4"><img :src="day.icon" :alt="day.description" /></div>
      </div> 
    </div>
  </div>
</template>

<script>
import axios from "axios";
import "@/pages/index.css";

export default {
  name: 'IndexPage',

  data() {
    return {
      havadurumlari: [],
      city: "",
      weather: null,
      forecast: []
    };
  },
  
  methods: {
    async asyncData() {
      try {
        const apiKey = "1725f4c3e35e4ee08a1151139240712";
        const weatherResponse = await axios.get(`https://api.weatherapi.com/v1/current.json?q=${this.city}&key=${apiKey}`);
        const forecastResponse = await axios.get(`https://api.weatherapi.com/v1/forecast.json?q=${this.city}&key=${apiKey}&days=4`);

        this.weather = {
          temperature: weatherResponse.data.current.temp_c,
          description: weatherResponse.data.current.condition.text,
          country: weatherResponse.data.location.country,
          name: weatherResponse.data.location.name,
        };

        if (forecastResponse.data.forecast && forecastResponse.data.forecast.forecastday) {
          this.forecast = forecastResponse.data.forecast.forecastday.map(day => ({
            date: day.date,
            temperature: day.day.avgtemp_c,
            description: day.day.condition.text,
            icon: day.day.condition.icon,
          }));
        } else {
          console.error("Tahmin verisi bulunamadı!");
        }

        this.havadurumlari = [];
        if (this.weather.description === "Partly cloudy") {
          this.havadurumlari.push({ id: 1, name: 'Güneşli-Bulutlu', image: 'resim1.jpg' });
        } else if (this.weather.description === "Clear" || this.weather.description === "Sunny") {
          this.havadurumlari.push({ id: 1, name: 'Güneşli', image: 'resim2.jpg' });
        } else if (this.weather.description === "Rain" || this.weather.description === "Moderate rain") {
          this.havadurumlari.push({ id: 1, name: 'Yağmurlu', image: 'resim3.jpg' });
        } else if (this.weather.description === "Light rain") {
          this.havadurumlari.push({ id: 1, name: 'Şimşekli Yağmurlu', image: 'resim4.jpg' });
        } else if (this.weather.description === "Snowy" || this.weather.description === "Blizzard") {
          this.havadurumlari.push({ id: 1, name: 'Karlı', image: 'resim5.jpg' });
        } else if (this.weather.description === "Overcast" || this.weather.description === "Cloudy") {
          this.havadurumlari.push({ id: 1, name: 'Bulutlu', image: 'resim6.jpg' });
        } else if (this.weather.description === "Thunderstorm") {
          this.havadurumlari.push({ id: 1, name: 'Fırtına', image: 'resim8.jpg' });
        } else if (this.weather.description === "Fog" || this.weather.description === "Mist") {
          this.havadurumlari.push({ id: 1, name: 'Sisli', image: 'resim9.jpg' });
        }
      } catch (error) {
        alert("Bir hata oluştu", error);
      }
    },

    async appendValue() {
      if (this.city === "") {
        alert("Lütfen bir şehir adı giriniz.");
        return;
      }
      
      this.havadurumlari = [];
      this.forecast = [];
      await this.asyncData();
      this.city = "";
    }
  }
}
</script>