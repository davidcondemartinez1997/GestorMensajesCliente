<template>
  <div id="maestrodetalle">
    <div id="lista">
      <h1>Tipos de mensajes</h1>
      <table v-if="tipos && tipos.length" class="table table-bordered">
        <thead class="thead-inverse">
          <tr>
            <th>Nombre</th>
            <th>Fichero</th>
            <th>Destacado</th>
            <th>Base</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="tipo of tipos" v-on:click="detail" v-bind:id="tipo.Id" v-bind:class="{ 'table-active': tipo.Id == idSeleccionado}">
            <td>{{tipo.Nombre}}</td>
            <td> {{tipo.Fichero}}</td>
            <td> {{tipo.Destacado}}</td>
            <td> {{tipo.Base}}</td>
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
  import Form from './FormTipoMensaje.vue'
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'

  export default {
    name: 'app',
    data () {
      return {
        tipos: undefined,
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
        this.tipos.forEach((p, index) => {
          if(p.Id == id){
            let tipo = {
              Nombre: p.Nombre,
              Director: p.Director,
              Destacado: p.Destacado,
              Base: p.Base,
              Id: p.Id
            }
            this.openDetail(tipo);
          }
        });
      },
      nuevo: function (e) {
        let tipo = {
            Nombre: undefined,
            Director: undefined,
            Destacado: undefined,
            Base: undefined,
            Id: -1
        }
        this.openDetail(tipo);
      },
      openDetail: function(tipo){
        new Vue({
          el: '#form',
          render: h => h(Form),
          data: {
            tipo:tipo,
            idSeleccionado: this.idSeleccionado
          },
        });
        EventBus.$on("seleccionarId",(id)=>{this.idSeleccionado = id});
      },
      init: function(){
        let url = config.address + 'TipoMensaje/';
        axios.get(url)
        .then(response => {
          this.tipos = response.data;
        })
      }

    },
    created() {
      this.init();
      EventBus.$on('updateTipoMensaje', () => {
        this.init();
      });

    }

  }
</script>

<style>
</style>
