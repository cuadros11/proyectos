<template>
  <div id="app">
    <header>
      <h1>Talonario</h1>
    </header>
    <main>
      <div class="modal-intro">
        <div class="intro">
          <div class="colortitulo" style="display: flex;">
            <h2>CONFIGURA EL TALONARIO</h2>
            <button type="button" class="btn-close" disabled aria-label="Close"></button>
          </div>
          <div class="input-group">
            <div v-if="alertMsg" class="alert alert-danger" role="alert">
              {{ alertMsg }}
            </div>
            <input type="text" id="premio" v-model="premio" placeholder="ingrese premio de la rifa">
            <input type="number" id="valor" v-model="valor" placeholder="ingrese valor de la rifa">
            <select name="loterias" id="loterias" v-model="loteria">
              <option value="" disabled>Seleccione una lotería</option>
              <option value="loteria1">Lotería 1</option>
              <option value="loteria2">Lotería 2</option>
              <option value="loteria3">Lotería 3</option>
              <option value="loteria4">Lotería 4</option>
              <option value="loteria5">Lotería 5</option>
              <option value="loteria6">Lotería 6</option>
              <option value="loteria7">Lotería 7</option>
            </select>
            <select name="boletas" id="boletas" v-model="boletas">
              <option value="" disabled>Seleccione la cantidad de boletas</option>
              <option value="boletas99">99 Boletas</option>
              <option value="boletas999">999 Boletas</option>
            </select>
            <input type="date" placeholder="Fecha de la rifa" id="fechaRifa" v-model="fechaRifa">
            <button style="background-color: #359469;" type="button" class="buttonr"
              @click="validarFormulario">Guardar</button>
          </div>
        </div>
      </div>
      <div class="contenedor">
        <div class="cont-informacion">
          <h2>Informacion del Talonario</h2>
        </div>
        <div>

        </div>
        <div class="cont-acciones">
          <h3 class="titulo-info">Acciones</h3>
          <div class="cuerpo-acciones">
            <button type="button" class="btn btn-primary btn-lg">Listar Boletas</button>
            <button type="button" class="btn btn-primary btn-lg">Personalizar</button>

          </div>
        </div>

      </div>
    </main>
    <footer>
      <p>&copy; 2025 Talonario</p>
    </footer>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const premio = ref('')
const valor = ref('')
const loteria = ref('')
const boletas = ref('')
const fechaRifa = ref('')
const alertMsg = ref('')

function validarFormulario() {
  if (!premio.value || isNaN(premio.value) || Number(premio.value) <= 0) {
    alertMsg.value = 'Por favor ingrese un valor numérico válido para el premio de la rifa.'
    return
  }
  if (!valor.value || isNaN(valor.value) || Number(valor.value) <= 0) {
    alertMsg.value = 'Por favor ingrese un valor válido para la boleta.'
    return
  }
  if (Number(valor.value) > Number(premio.value)) {
    alertMsg.value = 'El valor de la boleta no puede ser mayor al premio de la rifa.'
    return
  }
  if (!loteria.value) {
    alertMsg.value = 'Por favor seleccione una lotería.'
    return
  }
  if (!boletas.value) {
    alertMsg.value = 'Por favor seleccione la cantidad de boletas.'
    return
  }
  if (!fechaRifa.value) {
    alertMsg.value = 'Por favor seleccione la fecha de la rifa.'
    return
  }

}
</script>
