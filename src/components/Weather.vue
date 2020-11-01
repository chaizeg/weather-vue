<template>
  <div class="row">
    <div class="box">
      <img alt="Weather logo" src="../assets/weather.jpg">
      <br>
      Enter a city to check its current weather : 
      <input v-model="city">
      <br>
      <button @click="generate">Generate</button>
    </div>
      <div v-if="show" class="weather box">
        <h2>Current local time : {{time}}</h2>
        <h2>Weather in {{city}} - {{country}} :</h2>
        <img :src="weatherIcon">
        <br>
        It's {{temperature}} °C and it feels like {{feltTemperature}} °C
        <br>
        It's : {{currentState}}
      </div>
      <div v-if="err" class ="box">
        <h2>{{city}} is not a valid city.. Try again</h2>
      </div>
  </div>
</template>

<script>
export default {
  name: 'Weather',
  data(){
    return {
      city:  "",
      country: "",
      show: false,
      currentState: "",
      feltTemperature: 0,
      temperature: 0,
      weatherIcon: "",
      time: "",
      err: false
    }
  },
  methods : {
    generate(){
      const request = require('request');

      //make call to api
      const api_key =""; //create account on weatherstack to have an api key
      const options = {
          url: "http://api.weatherstack.com/current?access_key="+api_key+"&query="+this.city,
          method: 'GET'
        };

      request(options, (err, res, body) => {
      if (err) {
          this.show = false;
          this.err = true;
          return false;
      }
      if(body.length == 0) {
        return false;
      }
      var val = JSON.parse(body);
      if(typeof val === undefined){
        return false;
      }
      if(typeof val.error != "object"){
        this.err = false;
        this.show = true;
        this.currentState = val.current.weather_descriptions[0];
        this.temperature = val.current.temperature;
        this.feltTemperature = val.current.feelslike;
        this.weatherIcon = val.current.weather_icons[0];
        this.country = val.location.country;
        this.city = val.location.name;
        this.time = val.location.localtime;
      }
      else{
        this.show = false
        this.err = true
      }
    });
    }
  
}}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
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
input {
  margin: 40px 0 0;
}
button {
  margin: 20px 0 0;
}
.weather {
  margin: 60px 0 0;
}
img {
  /* margin-bottom: 40px; */
  margin: 40px 10px 40px 10px;
}
.row{
  display: flex;
}
.box {
  flex: 1;
  position: relative;
  margin: 1%;
  border-radius: 20px;
  padding-bottom: 20px;
  border-width: 2px;
  border-style: solid;
  border-color: rgba(100, 100, 100, 0.2);
}
</style>