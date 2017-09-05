<template>
  <div id="maestrodetalle">
    <div id="lista">
      <h1>Entradas</h1>
      <table v-if="entradas && entradas.length" class="table table-bordered table-hover">
        <thead class="thead-inverse">
          <tr>
            <th>Pelicula</th>
            <th>Precio</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="entrada of entradas" v-on:click="detail" v-bind:id="entrada.Id" v-bind:class="{ 'table-active': entrada.Id == idSeleccionado}">
            <td>{{entrada.Pelicula}}</td>
            <td> {{entrada.Precio}}</td>
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
        entradas: undefined,
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

        this.entradas.forEach((p, index) => {
          if(p.Id == id){
            let entrada = {
              Pelicula: p.Pelicula,
              Precio: p.Precio,
              Id: p.Id
            }
            this.openDetail(entrada);
          }
        });
      },
      nuevo: function (e) {
        this.idSeleccionado = undefined;
        let entrada =  {
          Pelicula: undefined,
          Precio: undefined,
          Id:-1
        }
        this.openDetail(entrada);
      },

      openDetail:function(entrada){
        new Vue({
          el: '#form',
          render: h => h(Form),
          data: {
            entrada:entrada,
            idSeleccionado: this.idSeleccionado
          },
        });
        EventBus.$on("seleccionarId",(id)=>{this.idSeleccionado = id});
      },
      init: function(){
        let url = config.address + 'Entrada/';
        axios.get(url)
        .then(response => {
          this.entradas = response.data;
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
      EventBus.$on('updateEntrada', (() => {
        this.init();
      }));


    }
  }
</script>

<style>
</style>
