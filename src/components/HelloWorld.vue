<template>
  <div class="container">
    
    <!-- Button trigger modal -->
<button type="button" class="btn btn-primary" data-bs-toggle="modal" data-bs-target="#exampleModal">
  Launch demo modal
</button>

<!-- Modal -->
<div class="modal fade" id="exampleModal" tabindex="-1" aria-labelledby="exampleModalLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title" id="exampleModalLabel">Modal title</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        ...
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
        <button type="button" class="btn btn-primary">Save changes</button>
      </div>
    </div>
  </div>
</div>

    <div class="hello">
      <h1>{{ msg }}</h1>
      <label>
        <VueSpinner size="100" color="red" />
      </label>
      <p>Ingresa el indicativo del cual te interesa conocer si el buro tiene tarjetas qsl bajo resguardo.</p>
      <div class="container">
        <div class="row">
          <div class="col">
            <form action="#">
              <div class="form-group">
                <label for="exallsignInput">Indicativo:</label>
                <input ref="name" v-on:keyup.enter="onEnter" v-model="callsign" type="input" class="form-control" id="exallsignInput" placeholder="indicativo">
              </div>
              <div class="form-group">
                <button :disabled="askingApi" type="button" class="btn btn-primary" @click="say()">
                  <span v-if="askingApi" class="spinner-border spinner-border-sm" role="status" aria-hidden="true"></span>
                  <span v-if="askingApi" class="sr-only">Loading...</span>
                  Buscar tarjetas qsl
                </button>
              </div>
            </form>
          </div>
        </div>
      </div>
      <div class="container">
        <p class="text">En caso de haber encontrado tarjetas qsl y quieres recuperarlas recuerda que el procedimieto se encuentra normado por el <a href="https://fmre.mx/actividades" target="_blank">Reglamento del QSL Bureau.</a></p>
        <p>Si quieres ponerte en contacto siempre puedes enviar un correo con tus comentarios a qslbureau@fmre.mx donde con gusto te atenderemos.</p>
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
    console.log('setup...')
    const params = (new URL(document.location)).searchParams;
    if(params?.get('confirm') != undefined && params?.get('confirm') != '') {
      console.log('not null');
      console.log(params?.get('confirm'));
      this.$refs.exampleModal.show();
    }
  },
  mounted() {
    this.focusInput();
  },
  data(){
    return {
      askingApi: false,
      showModal: true
    }
  },
  methods:{
    focusInput() {
      this.$refs.name.focus();
    },
    onEnter: function() {
       this.say();
    },
    say() {
      if(this.callsign == undefined || this.callsign === ''){
          Swal.fire({
            icon: 'error',
            title: 'Requerido',
            text:  'El indicativo es requerido'
          })
      } else {
        this.askingApi = true;
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
              text:  `No se encontraron QSL para el indicativo ${ obj.callsign }`
            })
          }
        })
        .catch(function(err) {
          console.log('Fetch Error :-S', err);
          Swal.fire({
            icon: 'error',
            title: 'No disponible',
            text:  'Por el momento el servicio no esta disponible'
          })
        })
        .finally(() => {
          console.log('runnning finally')
          this.askingApi = false;
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
