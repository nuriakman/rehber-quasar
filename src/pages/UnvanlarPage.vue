<template>
  <q-page class="q-pa-md">
    <div class="row items-center q-col-gutter-md q-mb-md">
      <div class="col-grow">
        <q-input
          v-model="arama"
          debounce="400"
          filled
          clearable
          placeholder="Unvan ara..."
          @update:model-value="getUnvanlar"
          :loading="yukleniyor"
          dense
          prepend-inner-icon="search"
        />
      </div>
      <div>
        <q-btn color="primary" icon="refresh" label="Yenile" @click="getUnvanlar" :loading="yukleniyor" class="q-mr-sm" />
        <q-btn color="positive" icon="add" label="Ekle" @click="acEkle" />
      </div>
    </div>

    <q-table
      :rows="unvanlar"
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
        <q-td :props="props">{{ formatTarih(props.row.created_at) }}</q-td>
      </template>
      <template #body-cell-updated_at="props">
        <q-td :props="props">{{ formatTarih(props.row.updated_at) }}</q-td>
      </template>
    </q-table>

    <UnvanDuzenleDialog
      v-model="duzenleAcik"
      :unvan="seciliUnvan"
      @kaydedildi="getUnvanlar"
    />

    <UnvanEkleDialog
      v-model="ekleAcik"
      @eklendi="getUnvanlar"
    />
  </q-page>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { api } from 'boot/axios'
import { date } from 'quasar'
import UnvanDuzenleDialog from 'components/UnvanDuzenleDialog.vue'
import UnvanEkleDialog from 'components/UnvanEkleDialog.vue'

const unvanlar = ref([])
const arama = ref('')
const yukleniyor = ref(false)
const duzenleAcik = ref(false)
const seciliUnvan = ref(null)
const ekleAcik = ref(false)

const kolonlar = [
  { name: 'id', label: 'ID', field: 'id', align: 'left', sortable: true },
  { name: 'unvan_adi', label: 'Unvan', field: 'unvan_adi', align: 'left', sortable: true },
  { name: 'created_at', label: 'Oluşturma', field: 'created_at', align: 'left' },
  { name: 'updated_at', label: 'Güncelleme', field: 'updated_at', align: 'left' },
  { name: 'actions', label: 'İşlemler', field: 'actions', align: 'right' },
]

function formatTarih(val) {
  if (!val) return '-'
  return date.formatDate(val, 'DD.MM.YYYY HH:mm')
}

async function getUnvanlar() {
  try {
    yukleniyor.value = true
    const params = {}
    if (arama.value && arama.value.trim().length > 0) params.q = arama.value.trim()
    const { data } = await api.get('/unvanlar', { params })
    unvanlar.value = data
  } finally {
    yukleniyor.value = false
  }
}

onMounted(() => {
  getUnvanlar()
})

function acDuzenle(row) {
  seciliUnvan.value = { ...row }
  duzenleAcik.value = true
}

function acEkle() {
  ekleAcik.value = true
}
</script>
