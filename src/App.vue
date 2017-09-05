<template>
  <div id="home" class="container-fluid">
    <header class="jumbotron text-center">
      <h1>Maestro Detalle de Cine</h1>
      <button type="button" id="peliculas" class="btn btn-md btn-outline-primary" v-on:click="openComponent">Peliculas</button>
      <button type="button" id="entradas" class="btn btn-md btn-outline-primary" v-on:click="openComponent">Entradas</button>
    </header>
    
    <div id="maestrodetalle" class="col-sm-4"></div>
    <div id="messages"></div>
  </div>
</template>

<script>
  import axios from "axios"
  import Vue from 'vue'
  import MaestroTipoMensaje from "./AppTipoMensaje.vue"
  import MaestroMensaje from "./AppMensaje.vue"
  import {EventBus} from './EventBus.js';
  export default {
    name: 'app',
    data () {
      return {

      }
    },
    methods: {
      openComponent: function(e){
        if($('#' + e.target.id).hasClass("active")){
          document.getElementById("maestrodetalle").innerHTML = "";
          $('button').removeClass("active");
        }else{
          $('button').removeClass("active");
          switch(e.target.id){
            case "peliculas": 
            $('#peliculas').addClass('active');
            new Vue({
              el: '#maestrodetalle',
              render: h => h(MaestroPeliculas)
            })

            break;
            case "entradas": 
            $('#entradas').addClass('active');
            new Vue({
              el: '#maestrodetalle',
              render: h => h(MaestroEntradas)
            })

            break;
          }
        }

        
      }

    },

  }
</script>

<style>

</style>