<template>
  <div class="text-white mb-8">
    <div class="places-input text-gray-800">
      <input type="search" id="address" placeholder="Choose a city..." />
      <p>
        Selected:
        <strong id="address-value">none</strong>
      </p>
    </div>
    <div
      class="weather-container font-sans md:w-128 max-w-lg rounded-lg overflow-hidden bg-gray-900 shadow-lg mt-8"
    >
      <div class="current-weather flex items-center justify-between px-6 py-8">
        <div class="flex flex-col md:flex-row items-center">
          <div>
            <div class="text-5xl font-semibold">{{ Math.round(currentTemp.actualTemp) }}°C</div>
            <div>Feels like {{ Math.round(currentTemp.feelsLike) }}°C</div>
          </div>
          <div class="md:mx-5">
            <div class="font-semibold">{{ currentTemp.summary }}</div>
            <div>{{ location.name }}</div>
          </div>
        </div>
        <div>
          <img :src="currentTemp.icon" width="90px" height="90px" />
        </div>
      </div>
      <!-- end current-weather -->

      <div class="future-weather text-sm bg-gray-800 px-6 py-8 overflow-hidden">
        <div
          v-for="(day, index) in daily"
          :key="day.dt"
          class="flex items-center"
          :class="{ 'mt-8': index > 0 }"
          v-if="index > 0 && index < 6"
        >
          <div class="w-1/6 text-lg text-gray-200">{{ toDayOfWeek(day.dt) }}</div>
          <div class="w-4/6 px-4 flex items-center">
            <div>
              <img :src="iconLink + day.weather[0].icon + '.png'" width="70px" height="70px" />
            </div>
            <div class="ml-3">{{ day.weather[0].description }}</div>
          </div>
          <div class="w-1/6 text-right">
            <div>{{ Math.round(day.temp.max) }}°C</div>
            <div>{{ Math.round(day.temp.min) }}°C</div>
          </div>
        </div>
      </div>
      <!-- end future-weather -->
    </div>
    <!-- end weather-container -->
  </div>
</template>

<script>
export default {
  mounted() {
    this.fetchData();
    this.fetchLocation();
  },
  data() {
    return {
      location: {
        name: "Chittagong",
        lat: 22.34551,
        lon: 91.837817
      },
      currentTemp: {
        actualTemp: "",
        feelsLike: "",
        summary: "",
        icon: ""
      },
      daily: [],
      iconLink: "http://openweathermap.org/img/wn/"
    };
  },
  methods: {
    fetchData() {
      fetch(`/api/weather?lat=${this.location.lat}&lon=${this.location.lon}`)
        .then(response => response.json())
        .then(data => {
          console.log(data);
          this.currentTemp.actualTemp = data.current.temp;
          this.currentTemp.feelsLike = data.current.feels_like;
          this.currentTemp.summary = data.current.weather[0].main;
          this.currentTemp.icon =
            this.iconLink + data.current.weather[0].icon + ".png";
          this.daily = data.daily;
        });
    },
    toDayOfWeek(timstamp) {
      const newDate = new Date(timstamp * 1000);
      const days = ["SUN", "MON", "TUE", "WED", "THU", "FRI", "SAT"];
      return days[newDate.getDay()];
    },
    fetchLocation() {
      var placesAutocomplete = places({
        appId: "plJVGGM4JGP5",
        apiKey: "f8e6b521e463819a1f803e190e3270ad",
        container: document.querySelector("#address")
      }).configure({
        type: "city",
        aroundLatLngViaIP: false
      });

      var $address = document.querySelector("#address-value");
      placesAutocomplete.on("change", e => {
        $address.textContent = e.suggestion.value;
        this.location.name = `${e.suggestion.name}, ${e.suggestion.country}`;
        this.location.lat = e.suggestion.latlng.lat;
        this.location.lon = e.suggestion.latlng.lng;
      });

      placesAutocomplete.on("clear", function() {
        $address.textContent = "none";
      });
    }
  },

  watch: {
    location: {
      handler(newValue, oldValue) {
        this.fetchData();
      },
      deep: true
    }
  }
};
</script>
