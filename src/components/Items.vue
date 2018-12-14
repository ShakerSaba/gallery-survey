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
      <button @click="toTop" class="myBtn" id="topbutton" style="border:solid white;font-size:28px">▲</button>

      <button @click="openModal" class="myBtn" id="submitModal" style="border:solid white;display:block;left:30px;font-size:28px">Submit</button>
      <div id="myModal" class="modal">
        <div class="modal-content">
          <div><span @click="closeModal" class="close">&times;</span></div>
          <br><br>
          <div style="text-align:left" class="col-6">
            Name:<br> <input style="width: 100%; font-size:20px" type="text" id="name" v-model="name">
          </div>
          <div style="text-align:left" class="col-6">
            Email:<br> <input style="width: 100%; font-size:20px" type="text" id="email" v-model="email">
          </div>
          <br><br>
          <button class="button" @click="submit" style="font-size:28px">Submit</button>
        </div>
      </div>

      <div class="row">
        <div class="col-12">
          <div class="banner">
            <h2><img style="width:130px" src="../assets/logo.png"> <b>Noursat Art Gallery Survey</b></h2>
            <p>Please choose <b>at least ten</b> of your favorite art pieces from the categories below. Ten choices minimum are required but feel free to choose more. When you have finished the survey, press the submit button below to enter your Name and Email and submit the survey.</p>
            <p>For additional information please contact Marlon Oneid at 613-799-5650 or by <a href="mailto:marlon.oneid@oneidengineering.com">Email</a></p>
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

      <div class="flex-container">
        <div
          v-for="(item, listIndex) in list"
          :key="listIndex"
          v-bind:class="{imageFrameA: !item.selected,imageFrameB: item.selected}"
          :style="'flex-grow:' + item.flex"
          v-if="item.group == group"
        >
          <label class="container">
            <img class="image" :src="getImgUrl(listIndex)"><br/>
            <b>{{ item.title }}</b><br>
            Width: {{ item.width }} Height: {{ item.height }}
            <input type="checkbox" :id="'checkbox' + listIndex" v-model="item.selected">
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
      index: null,
      name: '',
      email: '',
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
  created() {
    window.addEventListener('scroll', this.handleScroll);
  },
  methods: {
    openModal: function (){
      document.getElementById('myModal').style.display = "block";
      document.getElementById('submitModal').style.display = "none";
    },
    closeModal: function(){
      document.getElementById('myModal').style.display = "none";
      document.getElementById('submitModal').style.display = "block";
    },
    reset: function(){
      var self = this;
      for(var i=0;i<self.list.length;++i){
        self.list[i].selected = false;
      }
      self.name = '';
      self.email = '';
      self.msg = '';
      self.state = 'survey';
    },
    submit: function(){
      var results = [];
      var self = this;
      self.state = 'loading';
      
      if(!self.name || !self.email){
        window.alert("You must enter both your name and email to submit the survey!");
        self.state = 'survey';
        return;
      }
      var count = 0;
      for(var i=0;i<self.list.length;++i){
        results.push(self.list[i].selected);
        if(self.list[i].selected){
          count++;
        }
      }
      if(count < 10){
        window.alert("You must select at least 10 items before submitting!");
        self.state = 'survey';
        return;
      }
              
      axios({
        method : "POST",
        url : 'https://gallery-storage.herokuapp.com/results',
        data : {
          name: self.name,
          email: self.email,
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
    },
    getImgUrl(index) {
      var imgs = require.context('../assets/images/', true, /\.jpg$/);
      return imgs('./' + (index+1).toString().padStart(3,'0') + ".jpg");
    },
    handleScroll: function() {
      if (screen.width > 900 && (document.body.scrollTop > 400 || document.documentElement.scrollTop > 400)) {
        document.getElementById("topbutton").style.display = "block";
      } else if (screen.width <= 900 && (document.body.scrollTop > 700 || document.documentElement.scrollTop > 700)) {
        document.getElementById("topbutton").style.display = "block";
      } else {
        document.getElementById("topbutton").style.display = "none";
      }
    },
    toTop: function() {
      if(screen.width >= 900){
        document.body.scrollTop = 0;
        document.documentElement.scrollTop = 0;
      }else{
        document.body.scrollTop = 600;
        document.documentElement.scrollTop = 600;
      }
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
  flex: 0 1 24%;
  border: 2px solid #ffffff;
  background-color: #ffffff;
  border-radius: 2px;
  margin: 5px;
}
.imageFrameB {
  flex: 0 1 24%;
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
  cursor: pointer;
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
  padding: 50px;
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
.banner {
  border: 2px none rgb(112, 95, 95);
  padding: 20px 50px;
  border-radius: 3px;
  box-shadow: 5px 5px 2px grey;
  background-color: white;
  font-size: 18px;
  color: rgb(251, 255, 255);
  background-image: url('../assets/banner.jpg');
  text-shadow: 1px 1px #000000;
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
.container input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}
.checkmark {
  border: solid 2px rgb(43, 43, 43);
  position: absolute;
  bottom: 25px;
  left: 5px;
  height: 25px;
  width: 25px;
  background-color: #eee;
}
.container:hover input ~ .checkmark {
  background-color: #ccc;
}
.container input:checked ~ .checkmark {
  background-color: rgb(34, 143, 216);
}
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}
.container input:checked ~ .checkmark:after {
  display: block;
}
.container .checkmark:after {
  left: 7px;
  top: 3px;
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

@media only screen and (max-width: 768px) {
  [class*="col-"] {
    width: 100%;
  }
  [class*="imageFrame"] {
    flex: 1 1 100%;
  }
  .splash {
    padding: 15px;
    width: 90%;
  }
  h1{
    font-size: 26px
  }
  h2{
    font-size: 20px
  }
}

.myBtn {
  display: none; 
  position: fixed;
  bottom: 20px;
  right: 30px;
  z-index: 99;
  border: none;
  outline: none;
  background-color: rgb(81, 130, 221);
  color: white;
  cursor: pointer;
  padding: 15px;
  border-radius: 10px; 
  font-size: 18px;
}

.modal {
  display: none;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
}
.modal-content {
  margin: 15% auto; /* 15% from the top and centered */
  padding: 50px;
  width: 80%; /* Could be more or less, depending on screen size */
  border: 2px solid rgb(255, 255, 255);
  border-radius: 3px;
  box-shadow: 5px 5px 2px grey;
  background-color: white;
  font-size: 18px;
}
.close {
  color: #aaa;
  float: right;
  font-size: 36px;
  font-weight: bold;
}
.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

* {
  box-sizing: border-box;
}

</style>
