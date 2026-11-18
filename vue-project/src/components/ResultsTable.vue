<script setup>
defineProps({
  resultats: Array
})
</script>

<template>
  <div>
    <h2 class="mb-3">RESULTATS</h2>

    <div class="d-flex justify-content-center">
      <table class="table table-striped table-bordered w-auto text-center mb-0">
        <thead class="table-dark">
          <tr>
            <th>Tecla</th>
            <th>Objectiu</th>
            <th>Temps real</th>
            <th>Error</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="r in resultats" :key="r.tecla">
            <td>{{ r.tecla }}</td>
            <td>{{ r.objectiu }} s</td>
            <td>{{ r.tempsReal }} s</td>
            <td>{{ r.error }} s</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>