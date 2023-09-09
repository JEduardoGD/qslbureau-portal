<template>
  <div class="container">
    <div class="hello">
      <h1>{{ msg }}</h1>
      <label>
      </label>
      <p>Ingresa el indicativo del cual te interesa conocer si tiene tarjetas qsl bajo el resguardo del buro</p>
      <div class="container">
        <div class="row">
          <div class="col">
          </div>
          <div class="col">
            <form>
              <div class="form-group">
                <label for="exallsignInput">Indicativo {{ callsign }}</label>
                <input v-model="callsign" type="input" class="form-control" id="exallsignInput" placeholder="indicativo">
                <!--small id="emailHelp" class="form-text text-muted">We'll never share your email with anyone else.</small-->
              </div>
              <div class="form-group">
                <button type="button" class="btn btn-primary" @click="say('hello')">manatyy</button>
              </div>
              <div class="form-check">
              </div>
              <button type="submit" class="btn btn-primary">Submit</button>
            </form>
          </div>
          <div class="col">
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref } from 'vue';

let product = ref(null);

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  setup(){
  },
  methods:{
    say(message) {
      console.log(message);
      console.log(this.callsign);
      fetch('https://testapi.jasonwatmore.com/products/1')
      .then(response => response.json())
      .then(data => {
        this.product = data
      })
      .then(() => {
        console.log(product)
      })
    }
  }
}
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
</style>
