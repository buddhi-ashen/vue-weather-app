<template>
 

 <q-page class="flex column" :class="bgClass">

    <div class="col q-pt-lg q-px-md " >
          <q-input 
            v-model="search"
            @keyup.enter="getWeatherBySearch"
            placeholder="Search"
            dark
            borderless
            style="font-size: 20px;"
            
          >
        <template v-slot:before>
          <q-icon 
          @click="getLocation"
          name="share_location" 
          />
        </template>


        <template v-slot:append>
          <q-btn 
          @click="getWeatherBySearch"
          round 
          dense 
          flat 
          icon="search" />
        </template>
      </q-input>
    </div>

    <template v-if="weatherData" >
          <div class="col text-white text-center">
      <div class="text-h4 text-weight-light">
        {{ weatherData.name }}
      </div>
      <div class="text-h6 text-weight-light">
        <span>It's {{ weatherData.weather[0].main }} today</span>
      </div>
      <div class="text-h1 text-weight-thin q-my-lg relative-position">
        <div class="text-h6 text-weight-thin relative-position" >
          <span>Temp: </span>
        </div>
        <span>{{ Math.round(weatherData.main.temp) }}</span>
        <span class="text-h4 relative-position degree">&deg;C</span>
      </div>
      <div class="text-h6 text-weight-thin relative-position" style="font-size: 1.2em;">
        <span>WIND SPEED : {{ weatherData.wind.speed }} m/s</span>
      </div>
      <div class="text-h6 text-weight-thin relative-position" style="font-size: 1.2em;">
        <span>HUMIDITY: {{ weatherData.main.humidity }} g.m<sup>-3</sup></span>
      </div>
    </div>

    <div class="col text-center">
      <img :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`">
    </div>

    <div class="text-h6 text-weight-thin relative-position" 
    style="font-size: 1.2em;color: #fff;text-align: center;">
      <span>lat: {{ weatherData.coord.lat }} , long: {{ weatherData.coord.lon }}</span>
    </div>
    </template>

    <template v-else>
      <div class="col column text-center text-white">
        <div class="col text-h2 text-weight-thin">
          Blue Sky<br>Weather
        </div>
        <q-btn 
        @click="getLocation"
        class="col" 
        flat
        >
        <q-icon left size="2em" name="my_location" />
        <div class="mylocation">My Location</div>
    </q-btn>

      </div>
    </template>

    <div class="col skyline" ></div>
  </q-page>

</template>

<script>


export default {
  name: 'IndexPage',
  data() {
    return {
      search: '',
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: 'https://api.openweathermap.org/data/2.5/weather',
      blue: '42eec71a38fd79b5d3f2d66632ff1813'
    }
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith('n')) {
          return 'bg-night'
        }
        else{
          return 'bg-day'
        }
      }
    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show()

      if (this.$q.platform.is.electron) {
        this.$axios.get('https://api.ipbase.com/v1/json/').then(response => {
          this.lat = response.data.latitude
          this.lon = response.data.longitude
          this.getWeatherByCoords()
        })
      }
      else{
        navigator.geolocation.getCurrentPosition(position => {
        console.log('position:' , position)
        this.lat = position.coords.latitude
        this.lon = position.coords.longitude
        this.getWeatherByCoords()
      })

      }

      
    },
    getWeatherByCoords() {
      this.$q.loading.show()
      this.$axios(`${ this.apiUrl }?lat=${this.lat}&lon=${this.lon}&appid=${ this.blue }&units=metric`).then(response => {
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    },
    getWeatherBySearch() {
      this.$q.loading.show()
      this.$axios(`${ this.apiUrl }?q=${ this.search }&appid=${ this.blue }&units=metric`).then(response => {
        this.weatherData = response.data
        this.$q.loading.hide()
      })
    },
  }
}
</script>


<style lang="sass">
  @import url('https://fonts.googleapis.com/css2?family=Signika+Negative&display=swap')


  .q-page
    background-image: url("https://source.unsplash.com/random/?dark-nature")
    background-position: center center
    background-repeat: no-repeat
    background-size: cover
    font-family: 'Signika Negative', sans-serif
  .q-input 
    color: white
  .q-btn
    color: white
  .q-icon
    color: white
  .degree
    top: -44px
  .mylocation
    color: white
    font-size: 1rem
    text-transform: capitalize
    font-weight: 300
  .skyline
    flex: 0 0 100px
    background: url()
    background-size: contain
    background-position: center 

</style>