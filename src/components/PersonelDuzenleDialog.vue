<template>
  <q-dialog v-model="open" persistent>
    <q-card style="min-width: 420px; max-width: 90vw">
      <q-card-section>
        <div class="text-h6">Personel Düzenle</div>
        <div class="text-caption text-grey-7">ID: {{ personel?.id }}</div>
      </q-card-section>

      <q-separator />

      <q-card-section>
        <q-form @submit.prevent="kaydet">
          <div class="q-gutter-md">
            <q-input
              v-model="form.adi_soyadi"
              label="Adı Soyadı"
              :rules="[val => !!val || 'Zorunlu alan']"
              dense
              outlined
              autofocus
            />
            <q-select
              v-model="form.unvan_id"
              :options="unvanlar"
              option-label="unvan_adi"
              option-value="id"
              emit-value
              map-options
              label="Ünvan"
              :rules="[val => !!val || 'Zorunlu alan']"
              dense
              outlined
              :loading="unvanlarYukleniyor"
            />
            <q-select
              v-model="form.birim_id"
              :options="birimler"
              option-label="birim_adi"
              option-value="id"
              emit-value
              map-options
              label="Birim"
              :rules="[val => !!val || 'Zorunlu alan']"
              dense
              outlined
              :loading="birimlerYukleniyor"
            />
            <q-input
              v-model="form.telefon"
              label="Telefon"
              dense
              outlined
            />
            <q-input
              v-model="form.email"
              label="E-posta"
              type="email"
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
import { ref, watch, onMounted } from 'vue'
import { api } from 'boot/axios'

const props = defineProps({
  modelValue: { type: Boolean, default: false },
  personel: { type: Object, default: null },
})

const emit = defineEmits(['update:modelValue', 'kaydedildi'])

const open = ref(props.modelValue)
const yukleniyor = ref(false)
const unvanlar = ref([])
const birimler = ref([])
const unvanlarYukleniyor = ref(false)
const birimlerYukleniyor = ref(false)

const form = ref({ 
  adi_soyadi: '',
  unvan_id: null,
  birim_id: null,
  telefon: '',
  email: ''
})

watch(
  () => props.modelValue,
  (val) => {
    open.value = val
    if (val) hazirlaForm()
  }
)

watch(open, (val) => emit('update:modelValue', val))

watch(
  () => props.personel,
  () => {
    if (open.value) hazirlaForm()
  }
)

onMounted(async () => {
  await Promise.all([getUnvanlar(), getBirimler()])
})

async function getUnvanlar() {
  try {
    unvanlarYukleniyor.value = true
    const { data } = await api.get('/unvanlar')
    unvanlar.value = data
  } finally {
    unvanlarYukleniyor.value = false
  }
}

async function getBirimler() {
  try {
    birimlerYukleniyor.value = true
    const { data } = await api.get('/birimler')
    birimler.value = data
  } finally {
    birimlerYukleniyor.value = false
  }
}

function hazirlaForm() {
  form.value = {
    adi_soyadi: props.personel?.adi_soyadi || '',
    unvan_id: props.personel?.unvan_id || null,
    birim_id: props.personel?.birim_id || null,
    telefon: props.personel?.telefon || '',
    email: props.personel?.email || ''
  }
}

function kapat() {
  open.value = false
}

async function kaydet() {
  if (!props.personel?.id) return
  try {
    yukleniyor.value = true
    const payload = { ...form.value }
    await api.patch(`/personel/${props.personel.id}`, payload)
    emit('kaydedildi')
    kapat()
  } finally {
    yukleniyor.value = false
  }
}
</script>
