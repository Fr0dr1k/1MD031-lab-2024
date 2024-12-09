<template>
 <div class="burger">
    <img v-bind:src="burger.url" alt="Buger time" title="Burger image">
    <ul>
      <h2>{{burger.name}}</h2>
      <li>{{burger.kCal}} kCal</li>
      <li v-if="burger.gluten">Contains gluten</li>
      <li v-if="burger.lactose">Contains lactose</li>
    </ul>
    <div id="amountDisplay">
      <button v-on:click="decreaseBurgers" v-bind:class="{buttonColor: bgColor }">-</button>{{amountOrdered}}<button v-on:click="increaseBurgers">+</button>
    </div>
  </div>
</template>

<script>

export default {
  name: 'OneBurger',
  props: {
    burger: Object
  },
  data: function () {
    return {
      name:"",
      kCal:"",
      url:"",
      amountOrdered: 0,
      bgColor: true,
    }
  },
  methods: {
    decreaseBurgers() {
      if(this.amountOrdered>0){
        this.amountOrdered--;
      }
      if(this.amountOrdered<=0){
        this.bgColor = true;
      }
      this.$emit('orderedBurger', { name:   this.burger.name,
            amount: this.amountOrdered
          }
      );
    },
    increaseBurgers() {
      this.amountOrdered++;
      if(this.amountOrdered>0){
        this.bgColor = false;
      }
      this.amountOrdered += 1;
      this.$emit('orderedBurger', { name:   this.burger.name,
            amount: this.amountOrdered
          }
      );
    },
    addBurger: function () {
      this.amountOrdered += 1;
      this.$emit('orderedBurger', { name:   this.burger.name,
            amount: this.amountOrdered
          }
      );
    },
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .burger {
    margin: 1em;

  }

  .burger img{
    height: 15rem;
  }
  #amountDisplay{

  }

  .buttonColor{
    background-color: #575757;
  }

</style>