<template>
  <div id="maestrodetalle">
    <div id="lista">
      <h1>Peliculas</h1>
      <table v-if="peliculas && peliculas.length" class="table table-bordered">
        <thead class="thead-inverse">
          <tr>
            <th>Titulo</th>
            <th>Director</th>
            <th>Pais</th>
            <th>Genero</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="pelicula of peliculas" v-on:click="detail" v-bind:id="pelicula.Id" v-bind:class="{ 'table-active': pelicula.Id == idSeleccionado}">
            <td>{{pelicula.Titulo}}</td>
            <td> {{pelicula.Director}}</td>
            <td> {{pelicula.Pais}}</td>
            <td> {{pelicula.Genero}}</td>
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
  import Form from './FormPelicula.vue'
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'

  export default {
    name: 'app',
    data () {
      return {
        peliculas: undefined,
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
        this.peliculas.forEach((p, index) => {
          if(p.Id == id){
            let pelicula = {
              Titulo: p.Titulo,
              Director: p.Director,
              Pais: p.Pais,
              Genero: p.Genero,
              Id: p.Id
            }
            this.openDetail(pelicula);
          }
        });
      },
      nuevo: function (e) {
        let pelicula = {
            Titulo: undefined,
            Director: undefined,
            Pais: undefined,
            Genero: undefined,
            Id: -1
        }
        this.openDetail(pelicula);
      },
      openDetail: function(pelicula){
        new Vue({
          el: '#form',
          render: h => h(Form),
          data: {
            pelicula:pelicula,
            idSeleccionado: this.idSeleccionado
          },
        });
        EventBus.$on("seleccionarId",(id)=>{this.idSeleccionado = id});
      },
      init: function(){
        let url = config.address + 'Pelicula/';
        axios.get(url)
        .then(response => {
          this.peliculas = response.data;
        })
      }

    },
    created() {
      this.init();
      EventBus.$on('updatePelicula', () => {
        this.init();
      });

    }

  }
</script>

<style>
</style>
