<template>
  <q-dialog v-model="open" persistent>
    <q-card style="min-width: 420px; max-width: 90vw">
      <q-card-section>
        <div class="text-h6">Unvan Ekle</div>
      </q-card-section>

      <q-separator />

      <q-card-section>
        <q-form @submit.prevent="kaydet">
          <q-input
            v-model="form.unvan_adi"
            label="Unvan Adı"
            :rules="[val => !!val || 'Zorunlu alan']"
            dense
            outlined
            autofocus
          />
        </q-form>
      </q-card-section>

      <q-separator />

      <q-card-actions align="right">
        <q-btn flat label="Vazgeç" color="primary" :disable="yukleniyor" @click="kapat" />
        <q-btn unelevated label="Kaydet" color="primary" :loading="yukleniyor" @click="kaydet" />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script setup>
import { ref, watch } from 'vue'
import { api } from 'boot/axios'

const props = defineProps({
  modelValue: { type: Boolean, default: false },
})

const emit = defineEmits(['update:modelValue', 'eklendi'])

const open = ref(props.modelValue)
const yukleniyor = ref(false)
const form = ref({ unvan_adi: '' })

watch(
  () => props.modelValue,
  (val) => {
    open.value = val
    if (val) form.value = { unvan_adi: '' }
  }
)

watch(open, (val) => emit('update:modelValue', val))

function kapat() {
  open.value = false
}

async function kaydet() {
  try {
    yukleniyor.value = true
    await api.post('/unvanlar', { ...form.value })
    emit('eklendi')
    kapat()
  } finally {
    yukleniyor.value = false
  }
}
</script>
