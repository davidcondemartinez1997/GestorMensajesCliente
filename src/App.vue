<template>
  <div id="home" class="container-fluid">
    <header class="jumbotron text-center">
      <h1>Maestro Detalle del Gestor de Mensajes</h1>
      <button type="button" id="mensajes" class="btn btn-md btn-outline-primary" v-on:click="openComponent">Mensajes</button>
      <button type="button" id="tipoMensaje" class="btn btn-md btn-outline-primary" v-on:click="openComponent">Tipo Mensaje</button>
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
      //Estamos tocando el DOM-------------------------------------------------------------------------------------------
      openComponent: function(e){
        if($('#' + e.target.id).hasClass("active")){
          document.getElementById("maestrodetalle").innerHTML = "";
          $('button').removeClass("active");
        }else{
          $('button').removeClass("active");
          switch(e.target.id){
            case "mensajes": 
            $('#mensajes').addClass('active');
            new Vue({
              el: '#maestrodetalle',
              render: h => h(MaestroMensaje)
            })

            break;
            case "tipoMensaje": 
            $('#tipoMensaje').addClass('active');
            new Vue({
              el: '#maestrodetalle',
              render: h => h(MaestroTipoMensaje)
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