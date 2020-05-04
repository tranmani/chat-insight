<template>
  <div style="height:200px">
    <zingchart :height="'100%'" ref="myChart"
               :data="{
   shapes: [
    {
     type: 'zingchart.maps',
      options: {
        name: 'usa',
         ignore: ['AK','HI']
      }
    }]}" >
    </zingchart>
  </div>
</template>

<script>
import zingchartVue from 'zingchart-vue'
import 'zingchart/modules-es6/zingchart-maps.min.js'
import 'zingchart/modules-es6/zingchart-maps-usa.min.js'

export default {
  components: {
    zingchart: zingchartVue
  }
}
</script>
