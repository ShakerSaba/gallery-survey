<template>
  <div>
    <h2><b>Art Gallery Survey</b></h2>
    <p>Please select the art pieces you wish to see added to the art gallery. It is suggested you select around 10 items across <i>all</i> categories.</p>
    <br/>
    Name: <input type="text" id="name" v-model="name">
    Email: <input type="text" id="email" v-model="email">
    <button id="submitbutton" @click="submit">Submit</button>
    <br>
    <br>
        
    <h3>Select a Category:</h3>

    <button v-if="group == 'SMALLSTONE'" class="disabled">Smaller Stonework</button>
    <button v-else class="button" @click="group = 'SMALLSTONE'">Smaller Stonework</button>

    <button v-if="group == 'CARVING'" class="disabled">Stone Carvings</button>
    <button v-else class="button" @click="group = 'CARVING'">Stone Carvings</button>

    <button v-if="group == 'SCULPTURE'" class="disabled">Stone Statues</button>
    <button v-else class="button" @click="group = 'SCULPTURE'">Stone Statues</button>

    <button v-if="group == 'PAINTING'" class="disabled">Paintings</button>
    <button v-else class="button" @click="group = 'PAINTING'">Paintings</button>

    <button v-if="group == 'STAINGLASS'" class="disabled">Stained Glass</button>
    <button v-else class="button" @click="group = 'STAINGLASS'">Stained Glass</button>


    <gallery :images="images" :index="index" @close="index = null">
      <div v-if="index!=null">
        <h3 style="color=white">{{ list[index].title }}</h3>
      </div>
    </gallery>
    <div class="flex-container">
      <div
        v-for="(image, imageIndex) in images"
        :key="imageIndex"
        class="imageFrame"
        :style="'flex:' + list[imageIndex].flex + '1 24%'"
        v-if="list[imageIndex].group == group"
      >
        <img class="image" @click="index = imageIndex" :src="image">
        <br/>
        <input type="checkbox" :id="'checkbox' + imageIndex" v-model="list[imageIndex].selected">
        <label :for="'checkbox' + imageIndex">{{ list[imageIndex].title }}</label>
      </div>
    </div>
  </div>
</template>
 
<script>
  import VueGallery from 'vue-gallery';
  import items from '@/assets/items.json';
  import axios from "axios";
  
  export default {
    data: function () {
      return {
        list: items.items,
        images: items.images,
        index: null,
        name: '',
        email: '',
        group: ''
      };
    },
    methods: {
      submit: function(){
        var results = [];
        var self = this;

        for(var i=0;i>self.list.length;++i){
          results.push(self.list[i].selected);
        }
               
        axios.post('https://gallery-storage.herokuapp.com/results',
          {
            'name': self.name,
            'email': self.email,
            'results': results.toString()
          },
          {
            headers: {
              'Content-Type': 'application/x-www-form-urlencoded',
              timeout: 10000,
              headers: {
                'Access-Control-Allow-Origin' : '*',
                'Access-Control-Allow-Headers' : 'headers, Origin, X-Requested-With, Content-Type, Accept',
                'Access-Control-Allow-Credentials' : true
              }
            }
          }
        ).then(response => {
          console.log(response);
        })
        .catch(error => {
          console.log(error);
        });
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
  border: 1px solid #ebebeb;
  margin: 5px;
}

.flex-container {
  display: flex;
  flex-flow: row wrap;
  align-items: flex-start;
  align-content: flex-start;
}

.image {
  width: 96%;
  margin: 2%;
}

.button {
  background-color: rgb(81, 165, 221);
  color: white;
  border: none;
  border-radius: 4px;
  padding: 8px 20px;
  text-align: center;
  font-size: 16px;
  -webkit-transition-duration: 0.4s; /* Safari */
  transition-duration: 0.4s;
  border: 1px solid rgb(81, 165, 221);
}

.button:hover {
  background-color: rgb(255, 255, 255);
  color: rgb(0, 0, 0);
}

.disabled {
  background-color: rgb(255, 255, 255);
  color: rgb(0, 0, 0);
  border: none;
  border-radius: 4px;
  padding: 8px 20px;
  text-align: center;
  font-size: 16px;
  border: 1px solid rgb(81, 165, 221);
}
</style>
