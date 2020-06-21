<template>
  <div class="text-white mb-8">
    <div class="places-input text-gray-800">
      <input type="search" id="address" class="form-control" placeholder="Choose a city..." />

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
            <div class="text-6xl font-semibold">{{ currentTemp.actualTemp }}°C</div>
            <div>Feels like {{ currentTemp.feelsLike }}°C</div>
          </div>
          <div class="md:mx-5">
            <div class="font-semibold">{{ currentTemp.summary }}</div>
            <div>{{ location.name}}</div>
          </div>
        </div>
        <div>
          <canvas ref="iconCurrent" id="iconCurrent" width="96" height="96"></canvas>
        </div>
      </div>
      <!-- end current-weather -->

      <div class="future-weather text-sm bg-gray-800 px-6 py-8 overflow-hidden">
        <div class="flex items-center">
          <div class="w-1/6 text-lg text-gray-200">SAT</div>
          <div class="w-4/6 px-4 flex items-center">
            <div></div>
            <div class="ml-3">Mostly cloudy</div>
          </div>
          <div class="w-1/6 text-right">
            <div>26°C</div>
            <div>23°C</div>
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
  },
  data() {
    return {
      location: {
        name: "Chittagong",
        lat: 22.3569,
        lon: 91.7832
      },
      currentTemp: {
        actualTemp: "",
        feelsLike: "",
        summary: "",
        icon: ""
      }
    };
  },
  methods: {
    fetchData() {
      var skycons = new Skycons({ color: "pink" });

      fetch(`/api/weather?lat=${this.location.lat}&lon=${this.location.lon}`)
        .then(response => response.json())
        .then(data => {
          console.log(data);
          this.currentTemp.actualTemp = data.main.temp;
          this.currentTemp.feelsLike = data.main.feels_like;
          this.currentTemp.summary = data.weather[0].main;
          this.currentTemp.icon = data.weather[0].icon;
          skycons.add("iconCurrent", "partly-coludy-day");
        });
    }
  }
};
</script>
