<template>
  <q-page>

    <pre>
      {{ personeller }}
    </pre>

  </q-page>
</template>


<script setup>
import { api } from 'boot/axios'
import { onMounted } from 'vue'
import { ref } from 'vue'

const personeller = ref([])

async function getPersonel(params = {}) {
  const { data } = await api.get('http://localhost:8000/api/personel', { params })
  console.log(data)
  personeller.value = data
}

onMounted(() => {
  getPersonel()
})
</script>
