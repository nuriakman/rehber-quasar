<template>
  <q-dialog v-model="open" persistent>
    <q-card style="min-width: 420px; max-width: 90vw">
      <q-card-section>
        <div class="text-h6">Unvan Düzenle</div>
        <div class="text-caption text-grey-7">ID: {{ unvan?.id }}</div>
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
  unvan: { type: Object, default: null },
})

const emit = defineEmits(['update:modelValue', 'kaydedildi'])

const open = ref(props.modelValue)
const yukleniyor = ref(false)
const form = ref({ unvan_adi: '' })

watch(
  () => props.modelValue,
  (val) => {
    open.value = val
    if (val) hazirlaForm()
  }
)

watch(open, (val) => emit('update:modelValue', val))

watch(
  () => props.unvan,
  () => {
    if (open.value) hazirlaForm()
  }
)

function hazirlaForm() {
  form.value = {
    unvan_adi: props.unvan?.unvan_adi || '',
  }
}

function kapat() {
  open.value = false
}

async function kaydet() {
  if (!props.unvan?.id) return
  try {
    yukleniyor.value = true
    await api.patch(`/unvanlar/${props.unvan.id}`, { ...form.value })
    emit('kaydedildi')
    kapat()
  } finally {
    yukleniyor.value = false
  }
}
</script>
