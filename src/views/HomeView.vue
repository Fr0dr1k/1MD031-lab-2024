<template>
  <header>
    <h1>Burger time</h1>
  </header>
  <main>
    <section id="burgerSection">
      <!-- template in parent component -->
      <Burger v-for="burger in burgers"
              v-bind:burger="burger"
              v-bind:key="burger.name"
              v-on:orderedBurger="addToOrder($event)"/>
    </section>
    <section id="orderSection">
      <h2>Custmer info</h2>
      <p>The hamburger is very good, buy, buy, buy</p>
      <h2>Dilivery info</h2>
      <form>
        <p>
          <label for="name">Name</label><br>
          <input type="text" v-model="firstName" required="required" placeholder="First name">
        </p>
        <p>
          <input type="text" v-model="lastName" placeholder="Last name">
        </p>
        <p>
          <input type="email" v-model="eMail" required="required" placeholder="E-mail address">
        </p>

        <p>
          <label>Payment option</label>
          <select v-model="paymentInfo">
            <option>Card</option>
            <option>Swish</option>
            <option>Bitcoin</option>
            <option>Stealing</option>
          </select>
        </p>
      <section id = "map-container">
        <div id="map" v-on:click="addOrder">
          <div id="dot" v-bind:style="{ left: location.x + 'px', top: location.y + 'px'}">
            T
          </div>
        </div>
      </section>

        <button v-on:click="submitOrder" type="button" id="orderButton">
          Place order
          <img src="/img/sendIcon.png" alt="Place order">
        </button>
      </form>
    </section>
  </main>
</template>

<script>
import Burger from '../components/OneBurger.vue'
import io from 'socket.io-client'
import menu from "../assets/menu.json"

const socket = io("localhost:3000");

const burgers = []
menu.forEach(item => {
  const burger = new MenuItem(item.name, item.url, item.kCal, item.gluten, item.lactose);
  burgers.push(burger)
})

function MenuItem(name, url, kCal, gluten, lactose) {
  this.name = name;
  this.url = url;
  this.kCal = kCal;
  this.gluten = gluten;
  this.lactose = lactose;

}

export default {
  name: 'HomeView',
  computed: {
    Burger
  },
  components: {
    Burger
  },
  data: function () {
    return {
      burgers: burgers,
      firstName: "",
      lastName: "",
      eMail: "",
      paymentInfo: "",
      orderedBurgers: {},
      location: {
        x: 0,
        y: 0
      }
    }
  },
  methods: {
    getOrderNumber: function () {
      return Math.floor(Math.random()*100000);
    },
    addOrder: function (event) {
      var offset = {x: event.currentTarget.getBoundingClientRect().left,
                    y: event.currentTarget.getBoundingClientRect().top};

      this.location = {
        x: event.clientX - 10 - offset.x,
        y: event.clientY - 10 - offset.y
      }

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
                                details: { x: event.clientX - 10 - offset.x,
                                           y: event.clientY - 10 - offset.y },
                                orderItems: ["Beans", "Curry"]
                              }
                 );
    },

    setLocation: function (event) {
      this.location = {
        x: event.offsetX -10,
        y: event.offsetY -10
      }
    },

    submitOrder: function (event) {
      console.log("Order Details:");
      console.log("Name:", this.firstName);
      console.log("Email:", this.eMail);
      console.log("Payment Method:", this.paymentMethod);
      console.log(this.orderedBurgers)

      socket.emit("addOrder", { orderId: this.getOrderNumber(),
            details: {
              x: this.location.x,
              y: this.location.y
            },
            orderItems: ["Beans", "Curry"]
          }
      );

      /*const formattedOrderItems = [];
      for (const name in this.orderedBurgers) {
        const amount = this.orderedBurgers[name];
        formattedOrderItems.push(name + " (" + amount + ")");
      }
      console.log(formattedOrderItems);*/

      /*socket.emit("addOrder", {
        orderId: this.getOrderNumber(),
        details: { x: this.location.x, y: this.location.y,
          customer: {
            fullName: this.fullName,
            email: this.email,
            gender: this.gender,
            paymentMethod: this.paymentMethod,
          }},
        orderItems: Object.keys(this.orderedBurgers),
      });*/
    },

    addToOrder: function (event) {
      this.orderedBurgers[event.name] = event.amount;
    },
  }
}
</script>

<style>

  #map-container{
    outline: black solid 1px;
    margin: 4em 2em;
    overflow: scroll;
    height: 800px;
  }
  #map {
    width: 1920px;
    height: 1078px;
    background: url("/img/polacks.jpg");
    position: relative;
  }

  #dot{
    position: absolute;
    background: black;
    color: white;
    border-radius: 10px;
    width:20px;
    height:20px;
    text-align: center;
  }
  @import url('https://fonts.googleapis.com/css2?family=Agbalumo&family=Cormorant:wght@700&display=swap');
  body {
    font-size: 12pt;
    font-family: arial;
    margin-top: 0;
  }

  h1 {
    font-family: 'Agbalumo';
    font-size: 36pt;
  }
  main, header, footer, nav ul {
    max-width: 95%;
    margin: 0 auto 0 auto;
  }

  header {
    background-image: url("../img/polacks.jpg");
    background-size: contain;
    overflow: hidden;
    width: 100%;
    height: 200px;
    opacity: 0.5;
  }

  header h1 {
    width:40rem;
    margin: 0 auto;
    text-align: center;
  }

  nav ul {
    display: grid;
    grid-template-columns: repeat(auto-fill, 9.25em);
    gap: 1em;
    padding: 0;
  }

  nav li {
    display: block;
    background-color: grey;
    padding: 1em;
  }

  #burgerSection{
    color: white;
    background-color: black;
    display: flex;
    margin: 1em 0;

  }

  .burger img{
    height: 15rem;
  }

  #orderSection{
    border: black dotted 2px;
  }

  #orderButton{
    margin: 1em;
  }
  #orderButton img{
    height: 1em;
  }

  #orderButton:hover{
    background-color: pink;
    cursor: pointer;
  }
</style>