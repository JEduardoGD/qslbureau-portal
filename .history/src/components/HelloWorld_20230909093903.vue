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
                <label for="exallsignInput">Indicativo</label>
                <input type="text" class="form-control" id="exallsignInput" aria-describedby="emailHelp" placeholder="indicativo">
                <!--small id="emailHelp" class="form-text text-muted">We'll never share your email with anyone else.</small-->
              </div>
              <div class="form-group">
                <button @click="fetchData()">Save</button>
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
import { ref } from "vue";
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  methods : {
    manaty(){
      console.log('manaty');
    }
  },
  setup() {
    const data = ref(null);
    const loading = ref(true);
    const error = ref(null);

    function fetchData() {
  loading.value = true;
  // I prefer to use fetch
  // you can use use axios as an alternative
  return fetch('http://jsonplaceholder.typicode.com/posts', {
    method: 'get',
    headers: {
      'content-type': 'application/json'
    }
  })
    .then(res => {
      // a non-200 response code
      if (!res.ok) {
        // create error instance with HTTP status text
        const error = new Error(res.statusText);
        error.json = res.json();
        throw error;
      }

      return res.json();
    })
    .then(json => {
      // set the response data
      data.value = json.data;
    })
    .catch(err => {
      error.value = err;
      // In case a custom JSON error response was provided
      if (err.json) {
        return err.json.then(json => {
          // set the JSON response message
          error.value.message = json.message;
        });
      }
    })
    .then(() => {
      loading.value = false;
    });
}

    return {
      data,
      loading,
      error
    };
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
