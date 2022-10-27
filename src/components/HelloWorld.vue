<template>
    <form class="flex space-x-10 justify-center mt-5">
      <Datepicker v-model="fromDate" placeholder="Select Start Date" required ></Datepicker>
      <Datepicker v-model="toDate" placeholder="Select End Date" required ></Datepicker>
      <button type="submit" class="bg-orange-300  ">Submit</button>
    </form>
    
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
    <BarChart :labels="labels" :stats="stats"/>
</template>

<script>
    import Datepicker from '@vuepic/vue-datepicker'
    import '@vuepic/vue-datepicker/dist/main.css'
    import BarChart from './BarChart.vue'

    export default {
        components: { Datepicker, BarChart },
        data() {
            return {
                fromDate: '',
                toDate: '',
                fastestAsteroid: Object,
                closestAsteroid: Object,
                avgAsteroidSize: null,
                labels: [],
                stats: []
            };
        },
        mounted() {
            fetch('https://mocki.io/v1/d7692c87-8f1e-49c7-818c-b4470c373f00')
                .then(response => response.json())
                .then(data => {
                    this.fastestAsteroid = data.response.fastest_asteroid
                    this.closestAsteroid = data.response.closest_asteroid
                    this.avgAsteroidSize = data.response.avg_asteroid_size
                    data.response.days.forEach(day => {
                      this.labels.push(day)
                    });
                    let statsData = data.response.stats
                    // for(let i=0; i < data.response.stats.length; i++ ){
                    //     this.stats.push(data.response.stats[i])
                    // }
                    // statsData.forEach(element => {
                    //   this.stats.push(element)
                    // });
                    // this.labels = data.response.days
                    this.stats = statsData
                    // console.log(data.response.days)
                    // console.log(data.response.stats)

                })
        },
    }
</script>