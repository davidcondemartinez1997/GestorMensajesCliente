<template>
  <div id="form">
    <form v-if="seen" class="form-horizontal" onsubmit="return false" >

      <h1>Formulario Tipo de Mensaje <a class="close" v-on:click="close">&times;</a></h1>
      <div class="form-group">
        <label class="control-label">Nombre:</label>
        <input type="text" id="nombre" name="nombre" class="form-control" v-model:value="tipoMensaje.Nombre"/>   
      </div>
      <div class="form-group">

        <input type="checkbox" id="fichero" name="fichero"  v-model:value="tipoMensaje.Fichero"/> 
        <label class="">Permitir tener ficheros adjuntos</label>
      </div>

      <div class="form-group">
        <input type="checkbox" id="destacado" name="destacado" v-model:value="tipoMensaje.Destacado" />
        <label class="">Permitir crear mensajes destacados</label>
      </div>

      <div class="form-group">
        <label class="control-label">Basado en:</label>
        <input type="radio" name="ficheros" value="texto" v-model:value="tipoMensaje.Base"> Texto
        <input type="radio" name="ficheros" value="fichero"  v-model:value="tipoMensaje.Base"> Ficheros
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
        Fichero: this.tipoMensaje.Fichero,
        Destacado: this.tipoMensaje.Destacado,
        Base: this.tipoMensaje.Base,
      }
      let mensajeValidacion = this.isFormularioValido(data);
      if(mensajeValidacion == ''){
        if(this.tipoMensaje.Id == -1){

          axios.post(url, data)
          .then(response => {
            this.tipoMensaje.Id = response.data.Id;
            this.tipoMensaje.Nombre = response.data.Nombre;
            this.tipoMensaje.Fichero = response.data.Fichero;
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
          if(this.tipoMensajeBackUp.Nombre !== this.tipoMensaje.Nombre || this.tipoMensajeBackUp.Fichero !== this.tipoMensaje.Fichero  || this.tipoMensajeBackUp.Destacado !== this.tipoMensaje.Destacado || this.tipoMensajeBackUp.Base !== this.tipoMensaje.Base){
            data.Id = this.tipoMensaje.Id;
            axios.put(url + data.Id, data)
            .then(response => {
              this.fireEvent();
              EventBus.$emit("seleccionarId", response.data.Id);
            })
            .catch(response => {
              swal(
                '',
                'Ha ocurrido un error',
                'error'
                )
            })
          }else{
            swal(
              '',
              'Los datos no han cambiado',
              'info'
              )
          }

        }
      }else{
        swal(
          '',
          mensajeValidacion,
          'error'
          )
      }
    },
    isFormularioValido: function(data){
      if(!data.Nombre || data.Nombre.trim() == ''){
        return 'El nombre deberia estar relleno'
      }

      return '';
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
      Fichero:  this.tipoMensaje.Fichero,
      Destacado: this.tipoMensaje.Destacado,
      Base: this.tipoMensaje.Base
    };
    this.idSeleccionado = this.$parent.idSeleccionado;
  },
}
</script>

<style>
</style>
