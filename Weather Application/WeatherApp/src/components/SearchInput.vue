<template>
    <div>
    <!-- search field -->
    <form>
      <div class="bg-white border border-indigo-600/30 rounded-lg shadow-lg flex items-center">
        <i class="fa-solid fa-magnifying-glass p-2 text-indigo-600"></i>
        <input
          type="text"
          placeholder="Search for a place"
          class="rounded-r-lg p-2 border-0 outline-0 focus:ring-2 focus:ring-indigo-600 ring-inset w-full"
           v-model="searchTerm.query"
              @input="handleSearch"
        />
      </div>
    </form>
     <div class="bg-white my-2 rounded-lg shadow-lg">
      <div v-if="searchTerm.results !== null">
        <div v-for="place in searchTerm.results" :key="place.id">
          <button
            @click="getWeather(place.id)"
            class="px-3 my-2 hover:text-indigo-600 hover:font-bold w-full text-left"
          >
            {{ place.name }}, {{ place.region }}, {{ place.country }}
          </button>
        </div>
      </div>
    </div>
    </div>
</template>

<script setup>
import { reactive } from 'vue'
const emit = defineEmits(['place-data'])
const searchTerm = reactive({
  query: '',
  timeout: null,
  results: null
})
const handleSearch = () => {
   
  clearTimeout(searchTerm.timeout)
  searchTerm.timeout = setTimeout(async () => {
    if (searchTerm.query !== '') {
      const res = await fetch(
        `http://api.weatherapi.com/v1/search.json?key=3d4f4e6e43df43d9b4c155553242106&q=${searchTerm.query}`
      )

      const data = await res.json()
     
      searchTerm.results = data
    } else {
      searchTerm.results = null
    }
  }, 500)
}


const getWeather = async (id) => {
//    http://api.weatherapi.com/v1/forecast.json?key=3d4f4e6e43df43d9b4c155553242106&q=London&days=3&aqi=no&alerts=no
  const res = await fetch(
    `http://api.weatherapi.com/v1/forecast.json?key=3d4f4e6e43df43d9b4c155553242106&q=id:${id}&days=3&aqi=no&alerts=no`
  )

  const data = await res.json()

  emit('place-data', data)

  searchTerm.query = ''
  searchTerm.results = null
}



</script>

<style lang="scss" scoped>

</style>