<template>
  <q-page>

    <ol>
      <li
        v-for="personel in personeller"
        :key="personel.id"
      >
        {{ personel.adi_soyadi }},
        {{ personel.unvan.unvan_adi }},
        {{ personel.birim.birim_adi }},
        {{ personel.birim.konumu }}
      </li>
    </ol>

  </q-page>
</template>


<script setup>
import { api } from 'boot/axios'
import { onMounted } from 'vue'
import { ref } from 'vue'

const personeller = ref([])

async function getPersonel(params = {}) {
  const { data } = await api.get('http://localhost:8000/api/personel', { params })
  personeller.value = data
}

onMounted(() => {
  getPersonel()
})
</script>
