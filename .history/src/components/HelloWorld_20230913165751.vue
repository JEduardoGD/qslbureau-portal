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
            <form>
              <div class="form-group">
                <label for="exallsignInput">Indicativo {{ callsign }}</label>
                <input v-model="callsign" type="input" class="form-control" id="exallsignInput" placeholder="indicativo">
              </div>
              <div class="form-group">
                <button type="button" class="btn btn-primary" @click="say()">Buscar QSL's</button>
              </div>
            </form>
          </div>
        </div>
      </div>
      <div class="container">
        <p class="text">En caso de haber encontrado QSL's y quieres recuperarlas recuerda que el procedimieto se encuentra normado por el <a href="https://fmre.mx/actividades" target="_blank">Reglamento del QSL Bureau.</a></p>
        <p>Si quieres ponerte en contacto siempre puedes enviar un correo con tus comentarios a qslbureau @fmre.mx donde con gusto te atenderemos.</p>
      </div>
    </div>
  </div>
</template>

<script>
import moment from 'moment'
require('moment/locale/es')

const apiUrl = process.env.VUE_APP_API_URL;

import Swal from 'sweetalert2'

export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  setup(){
  },
  methods:{
    say() {
      if(this.callsign == undefined){
          Swal.fire({
            icon: 'error',
            title: 'Requerido',
            text:  'El indicativo es requerido'
          })
      } else {
        fetch(`${apiUrl}/qslsfor/${this.callsign}`)
        .then(response => response.json())
        .then(data => {
          let str = data.jsonPayload;
          let obj = JSON.parse(str);

          if(obj.count > 0){
            Swal.fire({
              icon: 'success',
              html: '<table class="table"><tbody>' +
                `<tr><td>Indicativo</td><td> ${ obj.callsign }</td>` +
                `<tr><td>QSLs encontradas</td><td> ${ obj.count }</td>` +
                `</tr><tr><td>QSL mas antigua registrada</td><td>${ moment(obj.oldest).format("DD MMM YYYY h:mm") }</td></tr>` +
                `</tr><tr><td>QSL mas reciente registrada</td><td>${ moment(obj.newest).format("DD MMM YYYY h:mm") }</td></tr>` +
                '</tbody></table>',
              })
          }
          if(obj.count === 0){
            Swal.fire({
              icon: 'warning',
              title: 'No encontrado',
              text:  `No se encontraron qsls para el indicativo ${ obj.callsign }`
            })
          }
        })
        .catch(function(err) {
          console.log('Fetch Error :-S', err);
        });
      }
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
