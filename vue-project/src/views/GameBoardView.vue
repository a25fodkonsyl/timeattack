<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import { useGameStore } from '@/stores/game'
import { socket } from '@/services/socket'
import KeyPad from '../components/KeyPad.vue'
import ResultsTable from '../components/ResultsTable.vue'
import ScoreBoard from '../components/ScoreBoard.vue'

const store = useGameStore()
const nom = ref('')

const tecles = ['A', 'S', 'D', 'F']

const tempsObjectiu = {
  A: 2,
  S: 4,
  D: 5,
  F: 7
}

const jugant = ref(false)
const resultats = ref([])

let tempsInici = 0

function entrar() {
  if (nom.value.trim() === '') return

  store.guardarNom(nom.value.trim())
}

function connectat() {
  console.log('Connectat al servidor!', socket.id)
}

function rebreClassificacio(dades) {
  console.log('Classificació rebuda:', dades)

  store.actualitzarClassificacio(dades)
}

onMounted(() => {
  socket.on('connect', connectat)
  socket.on('classificacio', rebreClassificacio)

  socket.connect()
})

onUnmounted(() => {
  socket.off('connect', connectat)
  socket.off('classificacio', rebreClassificacio)

  socket.disconnect()
})

function iniciarPartida() {
  resultats.value = []
  jugant.value = true
  tempsInici = performance.now()
}

function rebreTecla(tecla) {
  if (!jugant.value) return
  if (resultats.value.some(r => r.tecla === tecla)) return

  const tempsReal = (performance.now() - tempsInici) / 1000
  const objectiu = tempsObjectiu[tecla]
  const error = tempsReal - objectiu

  resultats.value.push({
    tecla: tecla,
    objectiu: objectiu,
    tempsReal: Number(tempsReal.toFixed(2)),
    error: Number(error.toFixed(2))
  })

  if (resultats.value.length === tecles.length) {
    jugant.value = false

    let errorTotal = 0

    for (let index = 0; index < resultats.value.length; index++) {
      const element = resultats.value[index]
      errorTotal = errorTotal + element.error
    }

    store.guardarPartida(Number(errorTotal.toFixed(2)))
  }
}
</script>

<template>
  <div class="min-vh-100 bg-primary-subtle py-5">
    <div class="container text-center">

      <!-- Formulario de nombre -->
      <div v-if="!store.nomJugador" class="card shadow mx-auto p-4" style="max-width: 500px">
        <h2 class="mb-4">Benvingut a TIME ATTACK!</h2>

        <input
          v-model="nom"
          class="form-control mb-3"
          placeholder="Introdueix el teu nom"
        />

        <button class="btn btn-primary btn-lg" @click="entrar">
          Entrar al joc
        </button>
      </div>

      <!-- Juego -->
      <div v-else>
        <h1 class="display-4 fw-bold mb-2">Time Attack</h1>
        <h2 class="mb-4">Hola, {{ store.nomJugador }}!</h2>

        <div class="card shadow p-4 mb-4">
          <ScoreBoard />
        </div>

        <div class="card shadow p-4 mb-4">
          <div>
            <button
              class="btn btn-success btn-lg mb-4"
              @click="iniciarPartida"
              :disabled="jugant"
            >
              Iniciar partida
            </button>
          </div>

          <KeyPad
            :tecles="tecles"
            :bloquejat="!jugant"
            :premudes="resultats.map(r => r.tecla)"
            @premuda="rebreTecla"
          />
        </div>

        <div
          v-if="!jugant && resultats.length === tecles.length"
          class="card shadow p-4"
        >
          <ResultsTable :resultats="resultats" />
        </div>
      </div>

    </div>
  </div>
</template>