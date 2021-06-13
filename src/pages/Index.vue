<template>
  <q-page class="flex column" :class="bgClass">
    <div class="col q-pt-lg q-px-md">
      <q-input 
      placeholder="搜索城市" 
      v-model="search" 
      dark
      borderless
      >
        <template v-slot:before>
          <q-btn round dense flat icon="my_location" @click="getLocation"/>
        </template>

        <template v-slot:hint>
          Field hint
        </template>

        <template v-slot:append>
          <q-btn round dense flat icon="search" @click="getWeatherByCity"/>
        </template>
      </q-input>


    </div>

    <template v-if="weatherData">
      <div class="col text-white text-center">
        <div class="text-h4 text-weight-light">
          {{weatherData.name}}
        </div> 
        <div class="text-h6 text-weight-light">
             {{weatherData.weather[0].description}}
        </div> 
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>  {{weatherData.main.temp}}</span>
          <span class="text-h4  relative-position degree">&deg;C</span>
        </div> 


      </div>  


      <div class="col text-center">  
        <img
        alt="img"
        :src="'http://openweathermap.org/img/wn/'+ weatherData.weather[0].icon +'@2x.png'"
        >

      </div> 
    </template>
    <template v-else>
      <div class="col column text-white text-center">

        <div class="col text-h2 text-weight-thin ">
          CYY<br>
          Weather
        </div> 
        <q-btn class="col" @click="getLocation">

          <q-icon left size="3em" name="my_location" />
          <div>定位我的位置</div>
          
        </q-btn>

      </div>  


    </template>
    <div class="col skyline">  
    </div>
    <!-- <img
      alt="Quasar logo"
      src="~assets/quasar-logo-full.svg"
    > -->
  </q-page>
</template>

<script>
export default {
  name: 'PageIndex',
  data() {
    return {
      search: '',
      weatherData: null,
      lat: '',
      lon: '',
      apiUrl: 'https://api.dlshr.com/coder/cyyWeather/getWeatherByCoords',
      apiCityUrl: 'https://api.dlshr.com/coder/cyyWeather/getWeatherByCity'
      
    }
  },
  computed: {
    bgClass() {
      if(this.weatherData){ 
        if(this.weatherData.weather[0].icon.endsWith('n')){
          return 'bg-night'
        }else{
          return 'bg-day'
        }

      }
    }
      
  },
  methods: {
    getLocation() {
      this.$q.loading.show()
      console.log('getLocation')
      
      navigator.geolocation.getCurrentPosition((position) => {
        
          console.log('position:',position)
          const lat = position.coords.latitude;
          const lng = position.coords.longitude;
          console.log(lat)
          console.log(lng)
          this.lat = position.coords.latitude
          this.lon = position.coords.longitude
          this.getWeatherByCoords()
      });
    },
    getWeatherByCoords() {
      if(!this.lat){
        console.log('Message')
        this.$q.notify('定位出现问题!')
        this.$q.loading.hide()
        return null
      }
      console.log('getWeatherByCoords')
      //08999bd29f3f448f2739fbcf789b4732
      this.$axios(this.apiUrl+'?lat='+this.lat+'&lon='+this.lon)
         .then(response=>{
               console.log('response:',response)
               this.weatherData = response.data
               this.$q.loading.hide()
         })
    },
    getWeatherByCity() {
      this.$q.loading.show()
      if(!this.search){
        console.log('search')
        this.$q.notify('请输入城市!')
        this.$q.loading.hide()
        return null
      }
      console.log('getWeatherByCity')
      //08999bd29f3f448f2739fbcf789b4732
      this.$axios(this.apiCityUrl+'?city='+this.search)
         .then(response=>{
               console.log('response:',response)
               this.weatherData = response.data
               if(this.weatherData.message && this.weatherData.message == 'city not found'){
                  this.$q.notify('查询失败，请检查输入城市!')
                  this.$q.loading.hide()
                  this.weatherData = null
               }

               this.$q.loading.hide()
         })
    }

  }
}
</script>

<style lang="scss">

  .q-page {
    background: linear-gradient(to bottom,
    #136a8a, #267871)  
  }
  .bg-night {
    background: linear-gradient(to bottom,
    #232526, #414345)  
  }
  .bg-day {
    background: linear-gradient(to bottom,
     #00b4db, #0083b0)  
  }
  .degree {
    top: -44px 
  }
  .skyline {
    flex: 0 0 100px;
    background: url(../statics/skyine2.png);
    background-size: contain;
    background-size: center bottom
  }
    
</style>


