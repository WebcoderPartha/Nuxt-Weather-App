<template>
  <section class="dark:bg-gray-800 dark:text-gray-100 h-[830px]">
    <h2 class="text-center font-bold text-5xl font-mono pt-8 mb-6">
      Weather App
    </h2>
    {{ locationState }}
    <form @submit.prevent="onChangeLocation">
      <div class="max-w-[600px] mx-auto grid grid-cols-4 gap-3 overflow-hidden">
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
        <div v-if="locationState.length > 0" class="relative -top-3 w-[450px] dark:bg-gray-700  dark:text-white p-5">
            <h2 @click="getWeatherHandle" :data-city="location.name" :data-region="location.region" :data-country="location.country" class="hover:bg-blue-700 hover:text-white py-1 px-1 text-center cursor-pointer" v-for="location in locationState">{{ location.name }}, {{ location.region }}, {{ location.country }}</h2>
        </div>
       
      </div>
    </form>
  </section>
</template>

<script setup>

const sicretKey = useRuntimeConfig().public.weatherApi

const query = ref('')

const getError = ref('')

const locationState = useLocationState()


const onChangeLocation = async () => {
      
  const { data:weatherLocation, error  } = await useFetch(
    `http://api.weatherapi.com/v1/search.json?key=${sicretKey}&q=${query.value}&aqi=yes`
  );
  console.log(weatherLocation.value)

  locationState.value = weatherLocation.value

};

const getWeatherHandle = async (e) => {
    const city = e.target.getAttribute('data-city')
    const region = e.target.getAttribute('data-region')
    const country = e.target.getAttribute('data-country')
    const { data:weatherResult, error, refresh} = await useFetch(`http://api.weatherapi.com/v1/current.json?key=${sicretKey}&q=${city},${region},{country}&aqi=yes`);
    locationState.value = []
    console.log(weatherResult.value)

}
</script>

<style lang="scss" scoped></style>
