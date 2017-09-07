<template>
  <div id="maestrodetalle">
    <div id="lista">
      <h1>Mensajes</h1>
      <table v-if="mensajes && mensajes.length" class="table table-bordered table-hover">
        <thead class="thead-inverse">
          <tr>
            <th>Destinatario</th>
            <th>TipoMensaje</th>
            <th>Asunto</th>
            <th>Contenido</th>
            <th>Archivo</th>
            <th>Fecha</th>
            <th>Destacado</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="mensaje of mensajes" v-on:click="detail" v-bind:id="mensaje.Id" v-bind:class="{ 'table-active': mensaje.Id == idSeleccionado}">
            <td> {{mensaje.Destinatario}} </td>
            <td> {{mensaje.TipoMensaje}}</td>
            <td> {{mensaje.Asunto}} </td>
            <td> {{mensaje.Contenido}} </td>
            <td> {{mensaje.Archivo}} </td>
            <td> {{new Date(mensaje.Fecha).toLocaleString()}} </td>
            <td v-if="mensaje.Destacado == true"> Activado </td>
            <td v-else>Desactivado</td>
          </tr>
        </tbody>
      </table>

      <button id="submit" class="btn btn-success btn-block" v-on:click="nuevo">Nuevo</button>
    </div>
    <div id="form" ></div>
  </div>

</template>

<script>
  import axios from 'axios';
  import {EventBus} from './EventBus.js';
  import Form from './FormMensaje.vue'
  import Vue from 'vue'

  export default {
    name: 'app',
    data () {
      return {
        mensajes: undefined,
        idSeleccionado: undefined
      }
    },
    methods: {
      detail: function (e) {
        let id = e.target.id;
        if(id == ""){
          id = e.target.parentNode.id;
        }
        this.idSeleccionado = id;

        this.mensajes.forEach((p, index) => {
          if(p.Id == id){
            let mensaje = {
              Destinatario: p.Destinatario,
              Asunto: p.Asunto,
              Contenido: p.Contenido,
              Archivo: p.Archivo,
              Destacado: p.Destacado,
              Id: p.Id
            }
            this.openDetail(mensaje);
          }
        });
      },
      nuevo: function (e) {
        this.idSeleccionado = undefined;
        let mensaje =  {
          Destinatario: undefined,
          Asunto: undefined,
          Contenido: undefined,
          Archivo: undefined,
          Destacado:undefined,
          Id:-1
        }
        this.openDetail(mensaje);
      },

      openDetail:function(mensaje){
        new Vue({
          el: '#form',
          render: h => h(Form),
          data: {
            mensaje:mensaje,
            idSeleccionado: this.idSeleccionado
          },
        });
        EventBus.$on("seleccionarId",(id)=>{this.idSeleccionado = id});
      },
      init: function(){
        let url = config.address + 'Mensaje/';
        axios.get(url)
        .then(response => {
          this.mensajes = response.data;
        })
        .catch(response => {
          swal(
            '',
            'Ha ocurrido un error',
            'error'
          )
        })
      }

    },
    created() {
      this.init();
      EventBus.$on('updateMensaje', (() => {
        this.init();
      }));
    }
  }
</script>

<style>
</style>
