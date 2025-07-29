<template>
  <div id="app">
    <header>
      <h1>Talonario</h1>
    </header>
    <main>
      <div v-if="showModal" class="modal-intro">
        <div class="intro">
          <div class="colortitulo" style="display: flex;">
            <h2>CONFIGURA EL TALONARIO</h2>
            <button type="button" class="btn-close" aria-label="Close" @click="showModal = false"></button>
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
      <!-- Modal para adquirir boleta -->
      <div v-if="showBoletaModal" class="modal-intro">
        <div class="intro">
          <div class="colortitulo" style="display: flex;">
            <h2>Adquirir Boleta N° {{ boletaSeleccionada }}</h2>
            <button type="button" class="btn-close" aria-label="Close" @click="showBoletaModal = false"></button>
          </div>
          <div class="input-group">
            <p>¿Desea adquirir la boleta número <strong>{{ boletaSeleccionada }}</strong>?</p>
            <button style="background-color: #359469;" type="button" class="buttonr" @click="abrirModalDatosComprador">
              Adquirir
            </button>
          </div>
        </div>
      </div>

      <!-- Modal para datos del comprador -->
      <div v-if="showDatosCompradorModal" class="modal-intro">
        <div class="intro">
          <div class="colortitulo" style="display: flex;">
            <h2>Datos del Comprador - Boleta N° {{ boletaSeleccionada }}</h2>
            <button type="button" class="btn-close" aria-label="Close" @click="showDatosCompradorModal = false"></button>
          </div>
          <div class="input-group">
            <div v-if="alertComprador" class="alert alert-danger" role="alert">
              {{ alertComprador }}
            </div>
            <input type="text" v-model="comprador.nombre" placeholder="Nombre del comprador" />
            <input type="text" v-model="comprador.telefono" maxlength="10" placeholder="Teléfono del comprador" />
            <input type="text" v-model="comprador.direccion" placeholder="Dirección del comprador" />
            <select v-model="comprador.estado">
              <option value="" disabled>Seleccione estado</option>
              <option value="pagada">Pagada</option>
              <option value="apartada">Apartada</option>
            </select>
            <button style="background-color: #359469;" type="button" class="buttonr" @click="guardarDatosComprador">
              Guardar
            </button>
          </div>
        </div>
      </div>

      <!-- Modal para gestionar boleta ocupada -->
      <div v-if="showGestionBoletaModal" class="modal-intro">
        <div class="intro">
          <div class="colortitulo" style="display: flex;">
            <h2>Boleta N° {{ boletaSeleccionada }}</h2>
            <button type="button" class="btn-close" aria-label="Close" @click="showGestionBoletaModal = false"></button>
          </div>
          <div class="input-group">
            <div v-if="gestionMsg" class="alert alert-success" role="alert">
              {{ gestionMsg }}
            </div>
            <div v-if="datosBoletaActual">
              <p><strong>Nombre:</strong> {{ datosBoletaActual.nombre }}</p>
              <p><strong>Teléfono:</strong> {{ datosBoletaActual.telefono }}</p>
              <p><strong>Dirección:</strong> {{ datosBoletaActual.direccion }}</p>
              <p><strong>Estado:</strong>
                <span v-if="datosBoletaActual.estado === 'pagada'" style="color: #00e676;">Pagada</span>
                <span v-else-if="datosBoletaActual.estado === 'apartada'" style="color: #e53935;">Apartada</span>
              </p>
            </div>
            <div style="margin-top: 16px;">
              <button v-if="datosBoletaActual && datosBoletaActual.estado === 'apartada'" type="button" class="buttonr" style="background:#00e676;margin-right:8px;" @click="marcarComoPagada">
                Marcar como Pagada
              </button>
              <button v-if="datosBoletaActual && datosBoletaActual.estado === 'pagada'" type="button" class="buttonr" style="background:#1976d2;margin-right:8px;" @click="marcarComoGanador">
                Dar como Ganador
              </button>
              <button type="button" class="buttonr" style="background:#bdbdbd;" @click="liberarBoleta">
                Liberar Boleta
              </button>
            </div>
          </div>
        </div>
      </div>

      <div class="contenedor">
        <div class="cont-informacion">
          <h2>Informacion del Talonario</h2>
          <div v-if="talonarioInfo">
            <p><strong>Premio:</strong> {{ talonarioInfo.premio }}</p>
            <p><strong>Valor Boleta:</strong> {{ talonarioInfo.valor }}</p>
            <p><strong>Lotería:</strong> {{ talonarioInfo.loteria }}</p>
            <p><strong>Fecha de la Rifa:</strong> {{ talonarioInfo.fechaRifa }}</p>
          </div>
          <div v-else>
            <p>No hay información registrada.</p>
          </div>
        </div>
        <div class="cant-boletas">
          <div v-if="boletasArray.length">
            <div style="display: flex; flex-wrap: wrap; gap: 15px;">
              <button
                v-for="num in boletasArray"
                :key="num"
                :style="{
                  width: '60px',
                  height: '60px',
                  borderRadius: '50%',
                  color: '#fff',
                  border: 'none',
                  display: 'flex',
                  alignItems: 'center',
                  justifyContent: 'center',
                  background: estadoBoletas[num] === 'pagada'
                    ? '#00e676'
                    : estadoBoletas[num] === 'apartada'
                      ? '#e53935'
                      : '#359469'
                }"
                @click="abrirModalBoleta(num)"
              >
                {{ num }}
              </button>
            </div>
          </div>
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
import { ref, reactive } from 'vue'

const premio = ref('')
const valor = ref('')
const loteria = ref('')
const boletas = ref('')
const fechaRifa = ref('')
const alertMsg = ref('')
const talonarioInfo = ref(null)
const boletasArray = ref([])
const showModal = ref(true)

const showBoletaModal = ref(false)
const boletaSeleccionada = ref(null)

const showDatosCompradorModal = ref(false)
const comprador = ref({
  nombre: '',
  telefono: '',
  direccion: '',
  estado: ''
})
const alertComprador = ref('')

// Guardar el estado y datos de cada boleta
const estadoBoletas = reactive({})
const datosBoletas = reactive({})

// Nuevo modal para ver/gestionar boleta ocupada
const showGestionBoletaModal = ref(false)
const datosBoletaActual = ref(null)
const gestionMsg = ref('')

function abrirModalBoleta(num) {
  boletaSeleccionada.value = num
  // Si la boleta está libre, abrir modal para adquirir
  if (!estadoBoletas[num]) {
    showBoletaModal.value = true
  } else {
    // Si está ocupada, abrir modal de gestión
    datosBoletaActual.value = datosBoletas[num]
    showGestionBoletaModal.value = true
    gestionMsg.value = ''
  }
}

function abrirModalDatosComprador() {
  showBoletaModal.value = false
  comprador.value = { nombre: '', telefono: '', direccion: '', estado: '' }
  alertComprador.value = ''
  showDatosCompradorModal.value = true
}

function guardarDatosComprador() {
  if (!comprador.value.nombre) {
    alertComprador.value = 'Ingrese el nombre del comprador.'
    return
  }
  if (!comprador.value.telefono || !/^\d{10}$/.test(comprador.value.telefono)) {
    alertComprador.value = 'El teléfono debe tener exactamente 10 números.'
    return
  }
  if (!comprador.value.direccion) {
    alertComprador.value = 'Ingrese la dirección del comprador.'
    return
  }
  if (!comprador.value.estado) {
    alertComprador.value = 'Seleccione el estado de la boleta.'
    return
  }
  // Guardar el estado y datos de la boleta seleccionada
  estadoBoletas[boletaSeleccionada.value] = comprador.value.estado
  datosBoletas[boletaSeleccionada.value] = { ...comprador.value }
  showDatosCompradorModal.value = false
}

function liberarBoleta() {
  if (boletaSeleccionada.value) {
    delete estadoBoletas[boletaSeleccionada.value]
    delete datosBoletas[boletaSeleccionada.value]
    showGestionBoletaModal.value = false
  }
}

function marcarComoPagada() {
  if (boletaSeleccionada.value && datosBoletas[boletaSeleccionada.value]) {
    estadoBoletas[boletaSeleccionada.value] = 'pagada'
    datosBoletas[boletaSeleccionada.value].estado = 'pagada'
    gestionMsg.value = '¡Boleta marcada como pagada!'
  }
}

function marcarComoGanador() {
  if (boletaSeleccionada.value && datosBoletas[boletaSeleccionada.value]) {
    gestionMsg.value = '¡Boleta marcada como ganadora!'
    // Aquí puedes agregar lógica adicional para el ganador si lo deseas
  }
}

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
  // Validar que la fecha sea posterior a 8 días del día actual
  const hoy = new Date()
  const fechaMinima = new Date(hoy.getFullYear(), hoy.getMonth(), hoy.getDate() + 8)
  const fechaSeleccionada = new Date(fechaRifa.value)
  if (fechaSeleccionada < fechaMinima) {
    alertMsg.value = 'La fecha de la rifa debe ser al menos 8 días después de hoy.'
    return
  }
  // Guardar la información
  talonarioInfo.value = {
    premio: premio.value,
    valor: valor.value,
    loteria: loteria.value,
    boletas: boletas.value,
    fechaRifa: fechaRifa.value
  }
  // Generar arreglo de boletas
  let cantidad = boletas.value === 'boletas99' ? 99 : 999
  boletasArray.value = Array.from({ length: cantidad }, (_, i) => i + 1)
  alertMsg.value = ''
  showModal.value = false // Cierra el modal
}
</script>
