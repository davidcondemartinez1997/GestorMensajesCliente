<template>
 <div id="form">
  <form v-if="seen" class="form-horizontal" onsubmit="return false">
   <h1 class="col-sm-12">Formulario Entrada <a class="close" v-on:click="close">&times;</a></h1>
   <div class="form-group">
    <label  class="control-label col-sm-2">Pelicula:</label>
    <input type="text" id="pelicula" name="pelicula" required="required" class="form-control" v-model:value="entrada.Pelicula"/>   
  </div>
  <div class="form-group">
    <label class="control-label col-sm-2">Precio:</label>
    <input type="text" id="precio" name="precio" class="form-control" required="required" v-model:value="entrada.Precio"/>   
  </div>
  <div class="form-group">
    <button id="submit" value="Enviar" class="form-control btn-success btn-block" v-on:click="enviar">Enviar</button>
    <button id="remove" value="Eliminar" class="form-control btn-success btn-danger" v-on:click="eliminar" v-if="entrada.Id !== -1">Eliminar</button>
  </div>


</form>
</div>
</template>

<script>
 import axios from 'axios';
 import {EventBus} from './EventBus.js';
 let url = config.address + 'Entrada/';
 export default {
  name: 'app',
  data () {
   return {
    entrada: {},
    seen: true,
    idSeleccionado: undefined
  }
},
methods: {
 enviar: function(){
  let data = {
   Pelicula: this.entrada.Pelicula,
   Precio: this.entrada.Precio,
 }
 if(data.Pelicula !== "" && data.Precio !== "" ){
   if(this.entrada.Id  == -1){
     axios.post(url ,data)
     .then(response => {
      this.entrada.Id = response.data.Id;
      this.entrada.Pelicula = response.data.Pelicula;
      this.entrada.Precio = response.data.Precio;
      this.entradaBackUp.Pelicula = response.data.Pelicula;
      this.entradaBackUp.Precio = response.data.Precio;
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
   else{
    data.Id = this.entrada.Id;
    if(this.entrada.Pelicula !== this.entradaBackUp.Pelicula || this.entrada.Precio !== this.entradaBackUp.Precio){

     axios.put(url + data.Id, data)
     .then(response => {
      this.fireEvent();
      EventBus.$emit("seleccionarId", response.data.Pelicula.Id);
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
}


},
close: function(){
  this.seen = false;
  EventBus.$emit("seleccionarId", undefined);
},
fireEvent: function(){
  swal(
    '',
    'Operacion completada con exito!',
    'success'
    );
  EventBus.$emit("updateEntrada", this.entrada);
},
eliminar: function(){
  if(this.entrada.Id != -1){
   this.seen = false;
   axios.delete(url + this.entrada.Id)
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

}

},

created() {
  this.seen = true;
  this.entrada = this.$parent.entrada;
  this.idSeleccionado = this.$parent.idSeleccionado;
  this.entradaBackUp = {
    Pelicula: this.entrada.Pelicula,
    Precio:  this.entrada.Precio
  }
}

}
</script>

<style>
</style>
