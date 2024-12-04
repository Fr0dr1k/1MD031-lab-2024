<template>
<div>
    <div>
    <h1>Burgers</h1>
    <Burger v-for="burger in burgers"
            v-bind:burger="burger" 
            v-bind:key="burger.name"/>
    </div>
    <div id="map" v-on:click="addOrder">
    click here
    </div>
</div>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'

const socket = io("localhost:3000");

const burgers = [
  new MenuItem("Cheeseburger", "public/img/cheeseburger.jpg",450,true,true),
  new MenuItem("Vego burger", "public/img/vegoBurgerjpg.jpg",350,true,false),
  new MenuItem("normal burger","public/img/normalBurger.jpg",650,true,true)
];

function MenuItem(name, imgUrl, kCal, gluten, lactose) {
  this.name = name;
  this.imgUrl = imgUrl;
  this.kCal = kCal;
  this.gluten = gluten;
  this.lactose = lactose;

}

export default {
  name: 'HomeView',
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: burgers
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};
      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    }
  }
}
</script>

<style>
  #map {
    width: 300px;
    height: 300px;
    background-color: red;
  }
</style>