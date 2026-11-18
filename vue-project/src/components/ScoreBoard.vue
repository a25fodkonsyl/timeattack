<script setup>
import { computed } from 'vue'
import { useGameStore } from '@/stores/game'

const store = useGameStore()

const classificacioOrdenada = computed(() => {
  return [...store.classificacio].sort((a, b) => a.errorTotal - b.errorTotal)
})
</script>

<template>
 <h2 class="mt-5 mb-3">CLASSIFICACIÓ GLOBAL</h2>

<div class="d-flex justify-content-center">
  <table class="table table-striped table-bordered w-auto text-center">
    <thead class="table-dark">
      <tr>
        <th>Jugador</th>
        <th>Error total</th>
      </tr>
    </thead>

    <tbody>
      <tr
        v-for="(resultat, index) in store.classificacio"
        :key="index"
      >
        <td>{{ resultat.nom }}</td>
        <td>{{ resultat.errorTotal }} s</td>
      </tr>
    </tbody>
  </table>
</div>
</template>