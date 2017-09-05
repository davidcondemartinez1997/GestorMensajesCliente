<template>
  <div id="form">
    <form v-if="seen" class="form-horizontal" onsubmit="return false" >

      <h1>Formulario Tipo de Mensaje <a class="close" v-on:click="close">&times;</a></h1>
      <div class="form-group">
        <label class="control-label">Nombre:</label>
        <input type="text" id="nombre" name="nombre" class="form-control" required="required" v-model:value="tipoMensaje.Nombre"/>   
      </div>
      <div class="form-group">
        <label class="control-label col-sm-2">Director:</label>
        <input type="text" id="director" name="director" class="form-control" required="required"  v-model:value="tipoMensaje.Director"/>   
      </div>

      <div id="countries" class="form-group">

      </div>

      <div class="form-group" id="FilmGenres">        
      </div>
      <div class="form-group">
        <button id="submit" value="Enviar" class="form-control btn-success btn-block" v-on:click="enviar">Enviar</button>
        <button id="remove" value="Eliminar" class="form-control btn-success btn-danger" v-on:click="eliminar" v-if="tipoMensaje.Id !== -1">Eliminar</button>
      </div>

    </form>
  </div>
</template>

<script>
  import axios from 'axios';
  import {EventBus} from './EventBus.js';
  import Vue from 'vue'

  let url = config.address + 'TipoMensaje/';
  export default {
    name: 'app',
    data () {
      return {
       tipoMensaje: {},
       seen: true,
       idSeleccionado: undefined
     }
   },
   methods: {
    enviar: function(){
      let preventUpdate = false;
      let data = {
        Nombre: this.tipoMensaje.Nombre,
        Director: this.tipoMensaje.Director,
        Destacado: this.tipoMensaje.Destacado,
        Base: this.tipoMensaje.Base,
        Id: this.tipoMensaje.Id
      }
      if(data.Nombre !== "" && data.Director !== "" && data.Destacado !== "" && data.Base !== ""){
        if(data.Id == -1){
          axios.post(url, data)
          .then(response => {
            this.tipoMensaje.Id = response.data.Id;
            this.tipoMensaje.Nombre = response.data.Nombre;
            this.tipoMensaje.Director = response.data.Director;
            this.tipoMensaje.Destacado = response.data.Destacado;
            this.tipoMensaje.Base = response.data.Base;
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
          if(this.tipoMensajeBackUp.Nombre !== this.tipoMensaje.Nombre || this.tipoMensajeBackUp.Director !== this.tipoMensaje.Director  || this.tipoMensajeBackUp.Destacado !== this.tipoMensaje.Destacado || this.tipoMensajeBackUp.Base !== this.tipoMensaje.Base){
            if(data !== this.tipoMensaje){
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
      EventBus.$emit("updateTipoMensaje", this.tipoMensaje);
    },

    eliminar: function(){
      if(this.tipoMensaje.Id != -1){
        this.seen = false;
        axios.delete(url + this.tipoMensaje.Id)
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
    this.tipoMensaje = this.$parent.tipoMensaje;
    this.tipoMensajeBackUp = {
      Nombre: this.tipoMensaje.Nombre,
      Director:  this.tipoMensaje.Director,
      Destacado: this.tipoMensaje.Destacado,
      Base: this.tipoMensaje.Base
    };
    this.idSeleccionado = this.$parent.idSeleccionado;
  },
}
</script>

<style>
</style>
