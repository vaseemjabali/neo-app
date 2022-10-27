<template>
    <form class="flex space-x-10 justify-center mt-5" v-on:submit.prevent="getNeoStats">
      <Datepicker :enableTimePicker="false" format="dd-MM-yyyy" v-model="fromDate" autoApply placeholder="Select Start Date" required ></Datepicker>
      <Datepicker :enableTimePicker="false" format="dd-MM-yyyy" v-model="toDate" autoApply placeholder="Select End Date" required ></Datepicker>
      <button type="submit" class="bg-orange-300">Submit</button>
    </form>
    <div v-if="display_loader" wire:loading class="bg-black fixed top-0 bottom-0 -mx-16 w-full h-screen z-10 overflow-hidden opacity-50 flex flex-col items-center justify-center">
      <svg class="animate-spin h-12 w-12 rounded-full bg-transparent border-2 border-transparent border-opacity-50 loader" viewBox="0 0 24 24"></svg>
    </div>
    <div>
        <div class="flex space-x-10 justify-center border-gray-900 mt-10">
        <div v-if="fastestAsteroid" class="border-2 border-indigo-500/50 shadow-xl rounded-md w-52 h-32">
          <span class="my-0.5"><strong>Fastest Asteroid</strong></span>
          <p class="mt-3"><i>#{{fastestAsteroid.fastest_asteroid_id}}</i></p>
          <label for="speed"><strong>Speed</strong></label>
          <p class="my-0.5">{{fastestAsteroid.fastest_asteroid_speed ?? '-'}}</p>
        </div>
        <div class="border-2 border-orange-500/50 shadow-xl rounded-md w-52 h-32">
          <span class="my-0.5"><strong>Closest Asteroid</strong></span>
          <p class="mt-3"><i>#{{closestAsteroid.closest_asteroid_id}}</i></p>
          <label for="speed"><strong>Distance</strong></label>
          <p class="my-0.5">{{closestAsteroid.closest_asteroid_distance ?? '-'}}</p>
        </div>
        <div class="border-2 border-red-500/50 shadow-xl rounded-md w-52 h-32">
          <label for="speed"><strong>Avg Asteroid Size</strong></label>
          <p class="mt-3">{{ avgAsteroidSize ?? '-' }}</p>
        </div>
      </div>
      <BarChart v-if="labels.length > 0" :labels="labels" :stats="stats"/>
    </div>
</template>

<script>
    import Datepicker from '@vuepic/vue-datepicker'
    import '@vuepic/vue-datepicker/dist/main.css'
    import BarChart from './BarChart.vue'
    import moment from 'moment'

    export default {
        components: { Datepicker, BarChart },
        data() {
            return {
                display_loader: false,
                fromDate: '',
                toDate: '',
                fastestAsteroid: Object,
                closestAsteroid: Object,
                avgAsteroidSize: null,
                labels: [],
                stats: []
            };
        },
        methods: {
          getNeoStats() {
            this.display_loader = true
            fetch(`http://127.0.0.1:8000/api/neo-stats?from_date=${this.getFormattedDate(this.fromDate)}&to_date=${this.getFormattedDate(this.toDate)}`)
                .then(response => response.json())
                .then(data => {
                  if(data.status){
                    this.data_found = true
                    this.fastestAsteroid = data.response.fastest_asteroid
                    this.closestAsteroid = data.response.closest_asteroid
                    this.avgAsteroidSize = data.response.avg_asteroid_size

                    data.response.days.forEach(day => {
                      this.labels.push(day)
                    });

                    let statsData = data.response.stats
                      for(let i=0; i < statsData?.length; i++ ){
                          this.stats[i] = statsData[i]
                      }
                  }
                  else{
                    this.emptyData()
                  }
                  this.display_loader = false
                }).catch(error => {
                  this.emptyData()
                  this.display_loader = false
                })
          },
          emptyData() {
            this.fastestAsteroid = Object
            this.closestAsteroid = Object
            this.avgAsteroidSize = null
            this.labels = []
            this.stats =[]
            return
          },
          getFormattedDate(date) {
            return date ? moment(String(date)).format('YYYY-MM-DD') : ''
          },
        }
    }
</script>

<style scoped>
.loader {
    border-top-color: white;
    border-right-color: white;
}
</style>