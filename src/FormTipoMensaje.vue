<template>
  <div id="form">
    <form v-if="seen" class="form-horizontal" onsubmit="return false" >

      <h1>Formulario Pelicula <a class="close" v-on:click="close">&times;</a></h1>
      <div class="form-group">
        <label class="control-label">Titulo:</label>
        <input type="text" id="titulo" name="titulo" class="form-control" required="required" v-model:value="pelicula.Titulo"/>   
      </div>
      <div class="form-group">
        <label class="control-label col-sm-2">Director:</label>
        <input type="text" id="director" name="director" class="form-control" required="required"  v-model:value="pelicula.Director"/>   
      </div>

      <div id="countries" class="form-group">

      </div>

      <div class="form-group" id="FilmGenres">        
      </div>
      <div class="form-group">
        <button id="submit" value="Enviar" class="form-control btn-success btn-block" v-on:click="enviar">Enviar</button>
        <button id="remove" value="Eliminar" class="form-control btn-success btn-danger" v-on:click="eliminar" v-if="pelicula.Id !== -1">Eliminar</button>
      </div>

    </form>
  </div>
</template>

<script>
  import axios from 'axios';
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'
  import FilmGenres from './FilmGenres.vue';
  import Countries from './Countries.vue';

  let url = config.address + 'Pelicula/';
  export default {
    name: 'app',
    data () {
      return {
       pelicula: {},
       seen: true,
       idSeleccionado: undefined
     }
   },
   methods: {
    enviar: function(){
      let preventUpdate = false;
      let data = {
        Titulo: this.pelicula.Titulo,
        Director: this.pelicula.Director,
        Pais: this.pelicula.Pais,
        Genero: this.pelicula.Genero,
        Id: this.pelicula.Id
      }
      if(data.Titulo !== "" && data.Director !== "" && data.Pais !== "" && data.Genero !== ""){
        if(data.Id == -1){
          axios.post(url, data)
          .then(response => {
            this.pelicula.Id = response.data.Id;
            this.pelicula.Titulo = response.data.Titulo;
            this.pelicula.Director = response.data.Director;
            this.pelicula.Pais = response.data.Pais;
            this.pelicula.Genero = response.data.Genero;
            this.fireEvent();
          })
          .catch(response => {
            swal(
              '',
              'Ha ocurrido un error',
              'error'
              )
          })
        }else{
          if(this.peliculaBackUp.Titulo !== this.pelicula.Titulo || this.peliculaBackUp.Director !== this.pelicula.Director  || this.peliculaBackUp.Pais !== this.pelicula.Pais || this.peliculaBackUp.Genero !== this.pelicula.Genero){
            if(data !== this.pelicula){
              axios.put(url + data.Id, data)
              .then(response => {
                this.fireEvent();
              })
              .catch(response => {
                swal(
                  '',
                  'Ha ocurrido un error',
                  'error'
                  )
              })
            }
          }else{
            swal(
              '',
              'Los datos no han cambiado',
              'info'
              )
          }

        }
      }
    },

    fireEvent: function(){
      swal(
        '',
        'Operacion completada con exito!',
        'success'
        )
      EventBus.$emit("updatePelicula", this.pelicula);
    },

    eliminar: function(){
      if(this.pelicula.Id != -1){
        this.seen = false;
        axios.delete(url + this.pelicula.Id)
        .then(response => {
          this.fireEvent();
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

    close: function(){
      this.seen = false;
      EventBus.$emit("seleccionarId", undefined);
    }
  },
  created() {
    this.seen = true;
    this.pelicula = this.$parent.pelicula;
    this.peliculaBackUp = {
      Titulo: this.pelicula.Titulo,
      Director:  this.pelicula.Director,
      Pais: this.pelicula.Pais,
      Genero: this.pelicula.Genero
    };
    this.idSeleccionado = this.$parent.idSeleccionado;
  },
  mounted(){
    console.log(this.pelicula.Genero);
    new Vue({
      el: '#FilmGenres',
      render: h => h(FilmGenres),
      data: {
        pelicula: this.pelicula
      }
    });
    new Vue({
      el: '#countries',
      render: h => h(Countries),
      data: {
        pais: this.pelicula.Pais
      }
    });
  }

}
</script>

<style>
</style>
