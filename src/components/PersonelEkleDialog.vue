<template>
  <q-dialog
    v-model="open"
    persistent
  >
    <q-card style="min-width: 420px; max-width: 90vw">
      <q-card-section>
        <div class="text-h6">Personel Ekle</div>
      </q-card-section>

      <q-separator />

      <q-card-section>
        <q-form
          @submit.prevent="kaydet"
          ref="formRef"
        >
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
              :rules="[val => !!val || 'Zorunlu alan']"
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
        <q-btn
          flat
          label="Vazgeç"
          color="primary"
          :disable="yukleniyor"
          @click="kapat"
        />
        <q-btn
          unelevated
          label="Kaydet"
          color="primary"
          :loading="yukleniyor"
          @click="kaydet"
        />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script setup>
import { ref, watch } from 'vue'
import { api } from 'boot/axios'
import { useQuasar } from 'quasar'

const $q = useQuasar()

const props = defineProps({
  modelValue: { type: Boolean, default: false },
})

// Ünvan ve birim listeleri
const unvanlar = ref([])
const birimler = ref([])
const unvanlarYukleniyor = ref(false)
const birimlerYukleniyor = ref(false)

const emit = defineEmits(['update:modelValue', 'eklendi'])

const open = ref(props.modelValue)
const yukleniyor = ref(false)
const formRef = ref(null)
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
    if (val) formSifirla()
  }
)

watch(open, (val) => {
  emit('update:modelValue', val)
  if (val) {
    getUnvanlar()
    getBirimler()
  }
})

async function getUnvanlar() {
  try {
    unvanlarYukleniyor.value = true
    const response = await api.get('/unvanlar')
    unvanlar.value = response.data
  } catch (error) {
    console.error('Ünvanlar yüklenirken hata oluştu:', error)
    $q.notify({
      type: 'negative',
      message: 'Ünvanlar yüklenirken bir hata oluştu'
    })
  } finally {
    unvanlarYukleniyor.value = false
  }
}

async function getBirimler() {
  try {
    birimlerYukleniyor.value = true
    const response = await api.get('/birimler')
    birimler.value = response.data
  } catch (error) {
    console.error('Birimler yüklenirken hata oluştu:', error)
    $q.notify({
      type: 'negative',
      message: 'Birimler yüklenirken bir hata oluştu'
    })
  } finally {
    birimlerYukleniyor.value = false
  }
}

function formSifirla() {
  form.value = {
    adi_soyadi: '',
    unvan_id: null,
    birim_id: null,
    telefon: '',
    email: ''
  }
}

function kapat() {
  open.value = false
}

async function kaydet() {
  const valid = await formRef.value?.validate()
  if (!valid) return

  try {
    yukleniyor.value = true
    await api.post('/personel', { ...form.value })
    $q.notify({ type: 'positive', message: 'Personel başarıyla eklendi' })
    emit('eklendi')
    kapat()
  } catch (error) {
    console.error('Personel eklenirken hata oluştu:', error)
    $q.notify({
      type: 'negative',
      message: 'Personel eklenirken bir hata oluştu',
      caption: error.response?.data?.message || 'Lütfen tekrar deneyin'
    })
  } finally {
    yukleniyor.value = false
  }
}
</script>
