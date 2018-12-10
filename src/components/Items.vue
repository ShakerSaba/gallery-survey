<template>
  <div>
    <div class="splash" v-if="state=='splash'">
      <h1><b>Noursat Art Gallery Survey</b></h1>
      <br><h2>Welcome!</h2>
      <p>Help us import the art pieces that are most appreciated by our community.</p>
      <p>Press the button below to begin.</p>
      <button class="button" @click="state = 'survey';group = 'SMALLSTONE';">Begin</button>
    </div>
    <div v-if="state=='survey'">
      <div class="row">
        <div class="col-9">
          <div class="top">
            <h2><b>Noursat Art Gallery Survey</b></h2>
            <p>Please choose at least ten of your favorite art pieces from the categories below. When you have finished the survey, enter your Name in the adjacent box and then hit the "Submit" button.</p>
            <p>For additional information please contact Marlon Oneid at 613-799-5650 or email: <a href="mailto:marlon.oneid@oneidengineering.com">marlon.oneid@oneidengineering.com</a></p>
          </div>
        </div>
        <div class="col-3">
          <div class="top" style="text-align:left">
            Name:<br> <input style="font-size:20px" type="text" id="name" v-model="name"><br>
            <br>
            <button class="button" @click="submit">Submit</button>
          </div>
        </div>
        <p class="col-12" style="text-align:left">This survey was designed by Shaker Saba. <a href="mailto:shakersilver@gmail.com">Email</a> | <a href="https://www.linkedin.com/in/shaker-s-743b478b/">LinkedIn</a></p>
      </div>
          
      <h3>Select a Category:</h3>
      <button v-if="group == 'SMALLSTONE'" class="disabled">Small Stonework</button>
      <button v-else class="button" @click="group = 'SMALLSTONE'">Small Stonework</button>

      <button v-if="group == 'CARVING'" class="disabled">Large Carvings</button>
      <button v-else class="button" @click="group = 'CARVING'">Large Carvings</button>

      <button v-if="group == 'SCULPTURE'" class="disabled">Large Statues</button>
      <button v-else class="button" @click="group = 'SCULPTURE'">Large Statues</button>

      <button v-if="group == 'PAINTING'" class="disabled">Oil Paintings</button>
      <button v-else class="button" @click="group = 'PAINTING'">Oil Paintings</button>

      <button v-if="group == 'STAINGLASS'" class="disabled">Stained Glass</button>
      <button v-else class="button" @click="group = 'STAINGLASS'">Stained Glass</button>

      <gallery :images="images" :index="index" @close="index = null"></gallery>
      <div class="flex-container">
        <div
          v-for="(image, imageIndex) in images"
          :key="imageIndex"
          v-bind:class="{imageFrameA: !list[imageIndex].selected,imageFrameB: list[imageIndex].selected}"
          :style="'flex:' + list[imageIndex].flex + '1 24%'"
          v-if="list[imageIndex].group == group"
        >
          <label class="container">
            <img class="image" :src="image"><br/>
            <b>{{ list[imageIndex].title }}</b><br>
            Width: {{ list[imageIndex].width }} Height: {{ list[imageIndex].height }}
            <input type="checkbox" :id="'checkbox' + imageIndex" v-model="list[imageIndex].selected">
            <span class="checkmark"></span>
          </label>
          <br/>
        </div>
      </div>
    </div>
    <div v-else-if="state=='loading'" class="splash">
      <div class="loader" style="horizontal"></div> 
    </div>
    <div v-else-if="state=='result'" style="width:70%"  class="splash">
      <div v-if="!msg">
        <p>Your survey answer has been accepted.</p>
        <p>Thank you for your time!</p>
      </div>
      <div v-else>
        <p>{{ msg }}</p>
      </div>
      <br/>
      <p>Try again? <button class="button" @click="reset">Go Back</button></p>
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
      group: '',
      state: 'splash',
      msg: ''
    };
  },
  computed:{
    frameSelected: function(){
      //console.log(imageIndex);
      var theClass = 'ImageFrameA';
      var self=this;
      
      return theClass;
    }
  },
  methods: {
    reset: function(){
      var self = this;
      for(var i=0;i<self.list.length;++i){
        self.list[i].selected = false;
      }
      self.name = '';
      self.msg = '';
      self.state = 'survey';
    },
    submit: function(){
      var results = [];
      var self = this;
      
      if(!self.name){
        window.alert("You must enter your name to submit the survey!");
        self.state = 'survey';
        return;
      }
      self.state = 'loading';

      for(var i=0;i<self.list.length;++i){
        results.push(self.list[i].selected);
      }
              
      axios({
        method : "POST",
        url : 'https://gallery-storage.herokuapp.com/results',
        data : {
          name: self.name,
          results: results.toString()
        },
        headers: {
          timeout: 10000,
          'Content-Type': 'application/x-www-form-urlencoded',
          'Access-Control-Allow-Origin' : '*',
          'Access-Control-Allow-Headers' : 'Authorization, Origin, X-Requested-With, Content-Type, Accept',
          'Access-Control-Allow-Credentials' : true
        }
      }).then(response => {
        console.log(response);
        self.state = 'result';
        if(typeof response.data == "string"){
          self.msg = response.data;
        }
      })
      .catch(error => {
        console.log(error);
        self.state = 'result';
        self.msg = error.message;
      });
    }
  },
  components: {
    'gallery': VueGallery
  },
  head: {
    meta: [
      {name: "viewport", content: "width=device-width, initial-scale=1.0"}
    ]
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

.imageFrameA {
  border: 2px solid #ffffff;
  background-color: #ffffff;
  border-radius: 2px;
  margin: 5px;
}

.imageFrameB {
  border: 2px solid #9ebcff;
  background-color: #9ebcff;
  border-radius: 2px;
  margin: 5px;
}

.flex-container {
  display: flex;
  flex-flow: row wrap;
  align-items: flex-start;
  align-content: flex-start;
}

.image {
  width: 98%;
  margin: 1%;
  margin-left:-35px
}

.button {
  background-color: rgb(81, 130, 221);
  color: white;
  border: none;
  border-radius: 4px;
  padding: 8px 20px;
  text-align: center;
  font-size: 16px;
  -webkit-transition-duration: 0.4s; /* Safari */
  transition-duration: 0.4s;
  border: 1px solid rgb(81, 130, 221);
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
  border: 1px solid rgb(81, 130, 221);
}

.loader {
  margin: auto;
  border: 16px solid #f3f3f3; /* Light grey */
  border-top: 16px solid #3498db; /* Blue */
  border-radius: 50%;
  width: 120px;
  height: 120px;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.splash {
  vertical-align: middle;
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border: 2px solid rgb(255, 255, 255);
  padding: 50px 50px;
  border-radius: 3px;
  box-shadow: 5px 5px 2px grey;
  background-color: white;
  font-size: 18px;
}
.top {
  border: 2px solid rgb(255, 255, 255);
  padding: 20px 50px;
  border-radius: 3px;
  box-shadow: 5px 5px 2px grey;
  background-color: white;
  font-size: 18px;
}

.container {
  display: block;
  position: relative;
  cursor: pointer;
  padding-left: 35px;
  font-size: 20px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

/* Hide the browser's default checkbox */
.container input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  border: solid 2px rgb(43, 43, 43);
  position: absolute;
  bottom: 5%;
  left: 2%;
  height: 25px;
  width: 25px;
  background-color: #eee;
}

/* On mouse-over, add a grey background color */
.container:hover input ~ .checkmark {
  background-color: #ccc;
}

/* When the checkbox is checked, add a blue background */
.container input:checked ~ .checkmark {
  background-color: rgb(34, 143, 216);
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.container input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.container .checkmark:after {
  left: 9px;
  top: 5px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

.row::after {
  content: "";
  clear: both;
  display: table;
}

.col-1 {width: 8.33%;}
.col-2 {width: 16.66%;}
.col-3 {width: 25%;}
.col-4 {width: 33.33%;}
.col-5 {width: 41.66%;}
.col-6 {width: 50%;}
.col-7 {width: 58.33%;}
.col-8 {width: 66.66%;}
.col-9 {width: 75%;}
.col-10 {width: 83.33%;}
.col-11 {width: 91.66%;}
.col-12 {width: 100%;}

[class*="col-"] {
  float: left;
  padding: 5px;
}

@media only screen and (max-width: 1000px) {
  /* For mobile phones: */
  [class*="col-"] {
    width: 100%;
  }
}

* {
  box-sizing: border-box;
}
</style>
