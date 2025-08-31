<template>
  <q-page class="q-pa-md">
    <div class="row items-center q-col-gutter-md q-mb-md">
      <div class="col-grow">
        <q-input
          v-model="arama"
          debounce="400"
          filled
          clearable
          placeholder="Personel adı veya soyadı ara..."
          @update:model-value="getPersoneller"
          :loading="yukleniyor"
          dense
          prepend-inner-icon="search"
        />
      </div>
      <div>
        <q-btn color="primary" icon="refresh" label="Yenile" @click="getPersoneller" :loading="yukleniyor" class="q-mr-sm" />
        <q-btn color="positive" icon="add" label="Ekle" @click="acEkle" />
      </div>
    </div>

    <q-table
      :rows="personeller"
      :columns="kolonlar"
      row-key="id"
      flat
      bordered
      :loading="yukleniyor"
      no-data-label="Kayıt bulunamadı"
    >
      <template #body-cell-actions="props">
        <q-td :props="props" class="text-right">
          <q-btn size="sm" flat color="primary" icon="edit" label="Düzenle" @click="acDuzenle(props.row)" />
        </q-td>
      </template>
      <template #body-cell-created_at="props">
        <q-td :props="props">
          {{ formatTarih(props.row.created_at) }}
        </q-td>
      </template>
      <template #body-cell-updated_at="props">
        <q-td :props="props">
          {{ formatTarih(props.row.updated_at) }}
        </q-td>
      </template>
    </q-table>

    <PersonelDuzenleDialog
      v-model="duzenleAcik"
      :personel="seciliPersonel"
      @kaydedildi="getPersoneller"
    />

    <PersonelEkleDialog
      v-model="ekleAcik"
      @eklendi="getPersoneller"
    />
  </q-page>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { api } from 'boot/axios'
import { date } from 'quasar'
import PersonelDuzenleDialog from 'components/PersonelDuzenleDialog.vue'
import PersonelEkleDialog from 'components/PersonelEkleDialog.vue'

const personeller = ref([])
const arama = ref('')
const yukleniyor = ref(false)
const duzenleAcik = ref(false)
const seciliPersonel = ref(null)
const ekleAcik = ref(false)

const kolonlar = [
  { name: 'id', label: 'ID', field: 'id', align: 'left', sortable: true },
  { name: 'adi_soyadi', label: 'Adı Soyadı', field: 'adi_soyadi', align: 'left', sortable: true },
  { name: 'unvan', label: 'Ünvan', field: 'unvan', align: 'left', sortable: true },
  { name: 'birim', label: 'Birim', field: 'birim', align: 'left', sortable: true },
  { name: 'telefon', label: 'Telefon', field: 'telefon', align: 'left' },
  { name: 'email', label: 'E-posta', field: 'email', align: 'left' },
  { name: 'created_at', label: 'Oluşturma', field: 'created_at', align: 'left' },
  { name: 'updated_at', label: 'Güncelleme', field: 'updated_at', align: 'left' },
  { name: 'actions', label: 'İşlemler', field: 'actions', align: 'right' },
]

function formatTarih(val) {
  if (!val) return '-'
  return date.formatDate(val, 'DD.MM.YYYY HH:mm')
}

async function getPersoneller() {
  try {
    yukleniyor.value = true
    const params = {}
    if (arama.value && arama.value.trim().length > 0) {
      params.q = arama.value.trim()
    }
    const { data } = await api.get('/personel', { params })
    personeller.value = data
  } finally {
    yukleniyor.value = false
  }
}

function acDuzenle(row) {
  seciliPersonel.value = { ...row }
  duzenleAcik.value = true
}

function acEkle() {
  ekleAcik.value = true
}

onMounted(() => {
  getPersoneller()
})
</script>
