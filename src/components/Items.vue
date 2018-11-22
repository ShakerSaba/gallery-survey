<template>
  <div>
    <h3>Art Gallery Survey</h3>
    <p>Please check the art pieces you wish to see added to the art gallery - it is suggested you select 10.</p>
    <br/>
    Name: <input type="text" id="name" v-model="name">
    <button id="submitbutton" @click="submit">Submit</button>
    <br>

    <gallery :images="images" :index="index" @close="index = null"></gallery>
    <div
      v-for="(image, imageIndex) in images"
      :key="imageIndex"
      class="imageFrame"
    >
      <div class="image" @click="index = imageIndex" :style="{ backgroundImage: 'url(' + image + ')', width: '300px', height: '200px' }"></div>
      <br/>
      <input type="checkbox" :id="'checkbox' + imageIndex" v-model="list[imageIndex].selected">
      <label :for="'checkbox' + imageIndex">{{ list[imageIndex].title }}</label>
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
        images: items.images,
        index: null,
        name: ''
      };
    },
    methods: {
      submit: function(){
        console.log("clicked");
      }
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
  display: flex;
  position: relative;
  align-items: center;
  justify-content:center;
  flex-wrap: wrap;
  position:relative;
  width: 300px;
  height: 300px;
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
