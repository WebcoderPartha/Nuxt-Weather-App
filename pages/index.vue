<template>
  <section class="dark:bg-gray-800 dark:text-gray-100 min-h-[830px] pb-8">
    <h2 class="text-center font-bold md:text-5xl text-3xl font-mono pt-8 mb-6">
      Weather App
      <!-- {{ idstate }} -->
    </h2>
 
    <form @submit.prevent="onChangeLocation">
      <div class="max-w-[600px] mx-auto grid grid-cols-4 gap-3 overflow-hidden px-5 md:px-0">
        <input
          type="text"
          id="first_name"
          class="rounded-full bg-gray-50 col-span-3 border border-gray-300 text-gray-900 text-sm block w-full p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white"
          placeholder="Location"
          required v-model="query"
        />
        <button
          type="submit"
          class="text-gray-900 bg-white border border-gray-300 focus:outline-none hover:bg-gray-100 focus:ring-4 focus:ring-gray-200 font-medium rounded-full text-sm px-5 py-2.5 mr-2 mb-2 dark:bg-gray-800 dark:text-white dark:border-gray-600 dark:hover:bg-gray-700 dark:hover:border-gray-600 dark:focus:ring-gray-700"
        >
          Search
        </button>
        
        <!-- {{ getData }} -->
        <div v-if="locationState?.length > 0" class="relative -top-3 w-[450px] dark:bg-gray-700  dark:text-white p-5">
            <h2 @click="getWeatherHandle" :data-city="location.name" :data-region="location.region" :data-country="location.country" class="hover:bg-blue-700 hover:text-white py-1 px-1 text-center cursor-pointer" v-for="location in locationState">{{ location.name }}, {{ location.region }}, {{ location.country }}</h2>
        </div>
       
      </div>
    </form>

    <div class="max-w-[1200px] mx-auto weather-container mt-20" v-if="currentWeather">
    <h2 class="md:text-4xl text-2xl text-center pb-4" v-if="currentWeather.location?.region">{{ currentWeather.location?.name }}, {{ currentWeather.location?.region }}, {{ currentWeather.location?.country }}</h2>
    <h2 class="md:text-4xl text-2xl text-center pb-4" v-else>{{ currentWeather.location?.name }}, {{ currentWeather.location?.country }}</h2>
    <h2 class="md:text-2xl text-sx text-center pb-4 mb-4">{{ moment(currentWeather.location?.localtime).format('DD MMMM, Y') }} | {{ moment(currentWeather.location?.localtime).format("hh:mm A") }}</h2>

    <div class="grid grid-cols-1 gap-6 md:grid-cols-2">
    <article class="md:mx-auto dark:shadow-gray-500 shadow-md md:py-8 mx-3" v-if="currentWeather">
      
        <div class="text-center p-4 mb-6">
            <h2 class="md:text-3xl text-2xl font-thin">Today Weather</h2>
        </div>
        <div class="grid grid-cols-2 gap-4">
            <div>
                
                <img :src="currentWeather.current?.condition.icon" class="ml-10 md:w-28" alt="">
                <span class="text-md md:ml-10 px-4">{{ currentWeather.current?.condition.text }}</span>
                
            </div>
            <div>
                <div class="weather-right pt-5 text-right pr-10 pb-4 md:pb-0">
                    <h3 class="md:text-xl text-sm mb-2">Wind: {{ currentWeather.current?.wind_kph }} kmph</h3>
                    <h3 class="tmd:text-xl text-sm mb-2">Precip: {{ currentWeather.current?.precip_mm }} kmph</h3>
                    <h3 class="tmd:text-xl text-sm mb-2">Pressure: {{ currentWeather.current?.pressure_mb }} kmph</h3>
                    <h1 class="md:text-4xl text-2xl">{{ currentWeather.current?.temp_c }} °c</h1>
                </div>
            </div>
        </div>

    </article>

    <article class="dark:shadow-gray-500 shadow-md md:py-8 mx-3 pb-4 md:pb-0" v-if="currentWeather">
        <div class="text-center p-4 mb-6">
            <h2 class="md:text-3xl text-2xl font-thin">Forecast Days</h2>
        </div>
        <div class="grid grid-cols-3 gap-2 text-center">
            <div v-for="forcast in getForeCast" class="text-center">
                <p class="text-center"> {{  moment(forcast.date).format('dddd') }}</p>
             
                    <img :src="forcast.day?.condition.icon" class="ml-6 md:ml-14" width="80" alt="">
                
                <p class="text-center">{{ forcast.day?.condition.text }}</p>
                <p class="text-center">{{ forcast.day?.maxtemp_c }} °c</p>
                
            </div>
       
        </div>

    </article>

    </div>
</div>

  <div class="max-w-[600px] mx-auto mt-20 text-center">
    <h3 class=" from-neutral-300">Design & Developed by <br/><span class=" font-bold">Parthadeb Mondal</span></h3>
    <p class="text-center">Version 1.0.0</p>
  </div>

  </section>
</template>

<script setup>
useHead({
  title: 'Weather App'
})

import moment from 'moment';

const sicretKey = useRuntimeConfig().public.weatherApi

const query = ref('')

const getError = ref('')

const locationState = useLocationState()
const currentWeather = useCurrentWeatherState()


// Get Weather Location
const onChangeLocation = async () => {
      
  const { data:weatherLocation, error  } = await useFetch(
    `https://api.weatherapi.com/v1/search.json?key=${sicretKey}&q=${query.value}&aqi=yes`
  );

  locationState.value = weatherLocation.value


};
// End Weather Location



// Get Current Weather
const getWeatherHandle = async (e) => {
    const city = e.target.getAttribute('data-city')
    const region = e.target.getAttribute('data-region')
    const country = e.target.getAttribute('data-country')
    const { data:weatherResult, error, refresh} = await useFetch(`https://api.weatherapi.com/v1/current.json?key=${sicretKey}&q=${city},${region},${country}&aqi=yes`);
    locationState.value = []
    query.value = ''
    currentWeather.value = weatherResult.value

}
// End Get Current Weather

// // User IP Address
// const {data:userIp} = await useFetch('https://api.ipify.org/?format=json')
// const {data: userIpDetail} = await useFetch(`http://ip-api.com/json/${userIp.value.ip}`)
// const { data:userCurrentWeather, error, refresh} = await useFetch(`http://api.weatherapi.com/v1/current.json?key=${sicretKey}&q=${userIpDetail.value.city},${userIpDetail.value.regionName},${userIpDetail.value.country}&aqi=yes`);
// currentWeather.value = userCurrentWeather.value

// const {data:forecastData} = await useFetch(`https://api.weatherapi.com/v1/forecast.json?key=${sicretKey}&q=${userIpDetail.value.city},${userIpDetail.value.regionName},${userIpDetail.value.country}&days=3&aqi=no&alerts=no`)
// console.log(forecastData.value.forecast?.forecastday)
// const getForeCast = useForeCast()
// getForeCast.value = forecastData.value.forecast.forecastday


// End User IP Address


</script>


<style lang="scss" scoped></style>
