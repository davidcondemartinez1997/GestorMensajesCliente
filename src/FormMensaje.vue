<template>
  <div id="form">
    <form v-if="seen" class="form-horizontal" onsubmit="return false">
      <h1 class="col-sm-12">Formulario Mensaje <a class="close" v-on:click="close">&times;</a></h1>
      <div class="form-group" v-if="tipos && tipos.length">
        <label  class="control-label">Tipo de mensaje:</label>
        <select id="tipo" name="tipoMensaje" class="form-control" v-on:change="seleccionarTipo" v-model="mensaje.TipoMensaje">
        <option v-for="tipoMensaje in tipos" v-bind:id="tipoMensaje.Nombre" v-bind:value="tipoMensaje.Nombre">{{tipoMensaje.Nombre}}</option>
        </select>
      </div>
      <div v-else>
        No existen tipos de mensaje creados, por favor crea uno
      </div>
      <div class="form-group">
        <label  class="control-label col-sm-2">Destinatario:</label>
        <input type="text" id="destinatario" name="destinatario" class="form-control" v-model:value="mensaje.Destinatario"/>   
      </div>
      <div class="form-group">
        <label class="control-label col-sm-2">Asunto:</label>
        <input type="text" id="asunto" name="asunto" class="form-control" v-model:value="mensaje.Asunto"/>   
      </div>
      <div class="form-group">
        <label class="control-label col-sm-2">Contenido:</label>
        <textarea rows="4" id="contenido" name="contenido" class="form-control" v-model:value="mensaje.Contenido"/>   
      </div>
      <div class="form-group" v-if="permiteFicheros == true">
        <label class="control-label col-sm-2">Archivo:</label>

        <input type="file" id="archivo" name="archivo" class="form-control" v-on:change="cambioArchivo"/>   
      </div>
      <div  v-if="permiteDestacados == true" class="form-group">
        <input type="checkbox" id="dest" value="Activado" v-model:value="mensaje.Destacado"/>
        <label for="dest">Permitir destacar mensaje</label>
      </div>
      <div class="form-group">
        <button id="submit" value="Enviar" class="form-control btn-success btn-block" v-on:click="enviar">Enviar</button>
        <button id="remove" value="Eliminar" class="form-control btn-success btn-danger" v-on:click="eliminar" v-if="mensaje.Id !== -1">Eliminar</button>
      </div>
    </form>
  </div>
</template>

<script>
  import axios from 'axios';
  import {EventBus} from './EventBus.js';
  let url = config.address + 'Mensaje/';
  export default {
    name: 'app',
    data () {
      return {
        mensaje: {},
        seen: true,
        idSeleccionado: undefined,
        tipos: undefined,
        permiteFicheros: true,
        permiteDestacados: true,
      }
    },
    methods: {
      enviar: function(){
        let fecha = new Date();
        let data = {
          Destinatario: this.mensaje.Destinatario,
          Asunto: this.mensaje.Asunto,
          Contenido: this.mensaje.Contenido,
          Archivo: this.mensaje.Archivo,
          Fecha: fecha.toISOString(),
          Destacado: this.mensaje.Destacado,
          TipoMensaje: this.mensaje.TipoMensaje
        }

        let mensajeValidacion = isFormularioValido(data);

        if(mensajeValidacion ==''){
          if(this.mensaje.Id  == -1){
            axios.post(url ,data)
            .then(response => {
              this.mensaje.Id = response.data.Id;
              this.mensaje.Destinatario = response.data.Destinatario;
              this.mensaje.Asunto = response.data.Asunto;
              this.mensaje.Contenido = response.data.Contenido;
              this.mensaje.Archivo = response.data.Archivo;
              this.mensaje.Destacado = response.data.Destacado;
              this.mensajeBackUp.Destinatario = response.data.Destinatario;
              this.mensajeBackUp.Asunto = response.data.Asunto;
              this.mensajeBackUp.Contenido = response.data.Contenido;
              this.mensajeBackUp.Archivo = response.data.Archivo;
              this.mensajeBackUp.Destacado = response.data.Destacado;
              this.fireEvent();
            })
            .catch(response => {
              swal(
                '',
                'Ha ocurrido un error al enviar',
                'error'
                )
            })
          }
          else{
            if(this.mensaje.Destinatario !== this.mensajeBackUp.Destinatario || this.mensaje.Asunto !== this.mensajeBackUp.Asunto || this.mensaje.Contenido !== this.mensajeBackUp.Contenido || this.mensaje.Archivo !== this.mensajeBackUp.Archivo || this.mensaje.Destacado !== this.mensajeBackUp.Destacado || this.mensaje.TipoMensaje !== this.mensajeBackUp.TipoMensaje){
              data.Id = this.mensaje.Id;
              axios.put(url + data.Id, data)
              .then(response => {
                this.fireEvent();
              })
              .catch(response => {
                swal(
                  '',
                  'Ha ocurrido un error al actualizar',
                  'error'
                  )
              })
            }
            else{
              swal(
                '',
                'Los datos no han cambiado',
                'info'
                )
            }     
          }
        }
        else{
          swal(
            '',
            mensajeValidacion,
            'error'
            )
        }
      },
      close: function(){
        this.seen = false;
        EventBus.$emit("seleccionarId", undefined);
      },
      seleccionarTipo: function(){
        var selectorTipo = document.getElementById("tipo");
        var nombre = selectorTipo.options[selectorTipo.selectedIndex].value;
        this.tipos.forEach((tipo, index)=>{
          if(tipo.Nombre == nombre){
            this.mensaje.TipoMensaje = this.tipos[index].Nombre;
            this.permiteDestacados = this.tipos[index].Destacado;
            this.permiteFicheros = this.tipos[index].Fichero;
          }
        });
      },
      fireEvent: function(){
        swal(
          '',
          'Operacion completada con exito!',
          'success'
          ).then( () =>  {
            EventBus.$emit("updateMensaje", this.tipoMensaje);
          }


          );
        },
        cambioArchivo: function(e){
          this.mensaje.Archivo = e.target.value;
        },
        eliminar: function(){
          if(this.mensaje.Id != -1){
            swal({
              title: '¿Quieres eliminar el mensaje?',
              type: 'warning',
              showCancelButton: true,
              confirmButtonColor: '#3085d6',
              cancelButtonColor: '#d33',
              confirmButtonText: 'Si, eliminar!',
              cancelButtonText: 'Cancelar'
            }).then( () => {
              this.seen = false;
              axios.delete(url + this.mensaje.Id)
              .then(response => {
                this.fireEvent();
                swal(
                  '',
                  'El mensaje ha sido borrado.',
                  'success'
                  )
              })
              .catch(response => {
                swal(
                  '',
                  'Ha ocurrido un error',
                  'error'
                  )
              })
            })


          }
        }
      },

      created() {
        this.seen = true;
        this.mensaje = this.$parent.mensaje;
        if(!this.mensaje.TipoMensaje){
          this.mensaje.TipoMensaje = {};
        }
        this.idSeleccionado = this.$parent.idSeleccionado;
        this.mensajeBackUp = {
          Destinatario: this.mensaje.fDestinatario,
          Asunto:  this.mensaje.Asunto,
          Contenido: this.mensaje.Contenido,
          Archivo:  this.mensaje.Archivo,
          Destacado: this.mensaje.Destacado
        };
        let url = config.address + 'TipoMensaje/';
        axios.get(url)
        .then(response => {
          this.tipos = response.data;
          this.mensaje.TipoMensaje = response.data[0].Nombre;
        })
      }
    }

    function isFormularioValido(data){
      let mensajeVal='';
      /*if(data.TipoMensaje || data.TipoMensaje == ""){
        return 'Debes seleccionar un tipo de mensaje. Si no existe ninguno crea uno en la pestaña superior Tipo Mensaje'
      }*/

      if(!data.Destinatario || data.Destinatario.trim() == ''){
        return 'El destinatario debe estar relleno.'
      }
      if(!data.Asunto || data.Asunto.trim() == ''){
        return 'El asunto debe estar relleno'
      }

      if(!data.Contenido || data.Contenido.trim() == ''){
        return 'El contenido debe estar relleno'
      }

      return '';
    } 
  </script>

  <style>
  </style>
