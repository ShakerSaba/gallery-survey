<template>
<div class="hello">
  <h3>Sanity Test</h3>
  <ul id="survey items" >
    <li v-for="item in list" v-bind:key="item.title + list.indexOf(item)">
      {{ item.title }}
    </li>
  </ul>
</div>
</template>

<script>
import items from '@/assets/items.json'

export default {
  name: 'SurveyItems',
  data () {
    return {
      list: items.items
    }
  },
  created () {

  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
