<template>
  <div>
    <h3>Sanity Test</h3>
    <ul id="survey items" >
      <li v-for="item in list" v-bind:key="item.title + list.indexOf(item)">
        {{ item.selected }}
      </li>
    </ul>
    <gallery :images="images" :index="index" @close="index = null"></gallery>
    <div
      v-for="(image, imageIndex) in images"
      :key="imageIndex"
      class="imageFrame"
    >
      <div class="image" @click="index = imageIndex" :style="{ backgroundImage: 'url(' + image + ')', width: '300px', height: '200px' }"></div>
      <br/>
      <input type="checkbox" id="checkbox" v-model="list[imageIndex].selected">
      <label for="checkbox">{{ list[imageIndex].title }}</label>
    </div>
  </div>
</template>
 
<script>
  import VueGallery from 'vue-gallery';
  import items from '@/assets/items.json'
  
  export default {
    data: function () {
      return {
        list: items.items,
        images: [
          'https://i.imgur.com/n3aTNd0.jpg',
          'https://i.imgur.com/RhJcDwN.jpg',
          'https://i.imgur.com/9geQa78.jpg',
          'https://i.imgur.com/xDqGJik.jpg',
          'https://i.imgur.com/Znl9uWL.jpg',
          'https://i.imgur.com/sPcSPj9.jpg',
          'https://i.imgur.com/XdF84qK.png',
          'https://i.imgur.com/XzfN2XB.jpg',
          'https://i.imgur.com/w2A6brw.png',
          'https://i.imgur.com/Zg6Kpka.png',
        ],
        index: null
      };
    },
 
    components: {
      'gallery': VueGallery
    },
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

.imageFrame {
  float: left;

  border: 1px solid #ebebeb;
  margin: 5px;
}

.image {
  float: center;
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center center;
  border: 1px solid #ebebeb;
  margin: 5px;
}
</style>
