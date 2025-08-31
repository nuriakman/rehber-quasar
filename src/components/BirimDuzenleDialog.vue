<template>
  <q-dialog v-model="open" persistent>
    <q-card style="min-width: 420px; max-width: 90vw">
      <q-card-section>
        <div class="text-h6">Birim Düzenle</div>
        <div class="text-caption text-grey-7">ID: {{ birim?.id }}</div>
      </q-card-section>

      <q-separator />

      <q-card-section>
        <q-form @submit.prevent="kaydet">
          <div class="q-gutter-md">
            <q-input
              v-model="form.birim_adi"
              label="Birim Adı"
              :rules="[val => !!val || 'Zorunlu alan']"
              dense
              outlined
              autofocus
            />
            <q-input
              v-model="form.konumu"
              label="Konumu"
              :rules="[val => !!val || 'Zorunlu alan']"
              dense
              outlined
            />
          </div>
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
  birim: { type: Object, default: null },
})

const emit = defineEmits(['update:modelValue', 'kaydedildi'])

const open = ref(props.modelValue)
const yukleniyor = ref(false)
const form = ref({ birim_adi: '', konumu: '' })

watch(
  () => props.modelValue,
  (val) => {
    open.value = val
    if (val) hazirlaForm()
  }
)

watch(open, (val) => emit('update:modelValue', val))

watch(
  () => props.birim,
  () => {
    if (open.value) hazirlaForm()
  }
)

function hazirlaForm() {
  form.value = {
    birim_adi: props.birim?.birim_adi || '',
    konumu: props.birim?.konumu || '',
  }
}

function kapat() {
  open.value = false
}

async function kaydet() {
  if (!props.birim?.id) return
  try {
    yukleniyor.value = true
    const payload = { ...form.value }
    await api.patch(`/birimler/${props.birim.id}`, payload)
    emit('kaydedildi')
    kapat()
  } finally {
    yukleniyor.value = false
  }
}
</script>
