<template>
  <q-dialog
    v-model="goster"
    persistent
  >
    <q-card
      class="q-pa-none"
      style="min-width: 380px; max-width: 600px; width: 100%; border-radius: 12px; overflow: hidden;"
    >
      <!-- Başlık Bölümü -->
      <q-card-section class="bg-primary text-white q-pb-sm">
        <div class="row items-center no-wrap">
          <q-avatar
            size="lg"
            color="white"
            text-color="primary"
            class="q-mr-md"
            icon="person"
          />
          <div class="text-h5 text-weight-bold text-truncate">
            {{ personel.adi_soyadi || 'İsimsiz Personel' }}
          </div>
        </div>
      </q-card-section>

      <!-- İçerik Bölümü -->
      <q-card-section class="q-pt-lg">
        <div class="row q-col-gutter-md">
          <!-- Sol Kolon -->
          <div class="col-12 col-sm-6">
            <!-- Birim Kartı -->
            <q-card
              flat
              bordered
              class="q-mb-md"
            >
              <q-card-section class="q-pa-sm bg-grey-2">
                <div class="text-caption text-grey-7">Birim</div>
              </q-card-section>
              <q-card-section class="q-pa-sm">
                <div class="row items-center">
                  <q-icon
                    name="business"
                    size="sm"
                    class="q-mr-sm text-primary"
                  />
                  <span>{{ personel.birim?.birim_adi || personel.birim_adi || 'Belirtilmemiş' }}</span>
                </div>
              </q-card-section>
            </q-card>



            <!-- Telefon Kartı -->
            <q-card
              flat
              bordered
              class="q-mb-md"
            >
              <q-card-section class="q-pa-sm bg-grey-2">
                <div class="text-caption text-grey-7">Telefon</div>
              </q-card-section>
              <q-card-section class="q-pa-sm">
                <div
                  v-if="personel.telefon"
                  class="row items-center"
                >
                  <q-icon
                    name="phone"
                    size="sm"
                    class="q-mr-sm text-primary"
                  />
                  <a
                    :href="'tel:' + personel.telefon"
                    class="text-primary"
                  >
                    {{ formatPhoneNumber(personel.telefon) }}
                  </a>
                </div>
                <div
                  v-else
                  class="text-grey-6"
                >Belirtilmemiş</div>
              </q-card-section>
            </q-card>

            <!-- E-posta Kartı -->
            <q-card
              flat
              bordered
              class="q-mb-md"
            >
              <q-card-section class="q-pa-sm bg-grey-2">
                <div class="text-caption text-grey-7">E-posta</div>
              </q-card-section>
              <q-card-section class="q-pa-sm">
                <div
                  v-if="personel.email"
                  class="row items-center"
                >
                  <q-icon
                    name="email"
                    size="sm"
                    class="q-mr-sm text-primary"
                  />
                  <a
                    :href="'mailto:' + personel.email"
                    class="text-primary"
                  >
                    {{ personel.email }}
                  </a>
                </div>
                <div
                  v-else
                  class="text-grey-6"
                >Belirtilmemiş</div>
              </q-card-section>
            </q-card>
          </div>

          <!-- Sağ Kolon -->
          <div class="col-12 col-sm-6">

            <!-- Unvan Kartı -->
            <q-card
              flat
              bordered
              class="q-mb-md"
            >
              <q-card-section class="q-pa-sm bg-grey-2">
                <div class="text-caption text-grey-7">Ünvan</div>
              </q-card-section>
              <q-card-section class="q-pa-sm">
                <div class="row items-center">
                  <q-icon
                    name="badge"
                    size="sm"
                    class="q-mr-sm text-primary"
                  />
                  <span>{{ personel.unvan?.unvan_adi || personel.unvan_adi || 'Belirtilmemiş' }}</span>
                </div>
              </q-card-section>
            </q-card>
            <!-- Dahili Kartı -->
            <q-card
              flat
              bordered
              class="q-mb-md"
            >
              <q-card-section class="q-pa-sm bg-grey-2">
                <div class="text-caption text-grey-7">Dahili</div>
              </q-card-section>
              <q-card-section class="q-pa-sm">
                <div class="row items-center">
                  <q-icon
                    name="call_split"
                    size="sm"
                    class="q-mr-sm text-primary"
                  />
                  {{ personel.dahili || 'Belirtilmemiş' }}
                </div>
              </q-card-section>
            </q-card>
          </div>
        </div>

        <!-- Notlar Bölümü -->
        <q-card
          v-if="personel.notlar"
          flat
          bordered
          class="q-mt-md"
        >
          <q-card-section class="q-pa-sm bg-grey-2">
            <div class="row items-center">
              <q-icon
                name="notes"
                size="sm"
                class="q-mr-xs"
              />
              <span class="text-caption text-grey-7">Notlar</span>
            </div>
          </q-card-section>
          <q-card-section class="q-pa-md">
            <div class="text-body2">
              {{ personel.notlar }}
            </div>
          </q-card-section>
        </q-card>
      </q-card-section>

      <!-- Alt Butonlar -->
      <q-card-actions
        align="right"
        class="q-pa-md bg-grey-1"
      >
        <q-btn
          flat
          label="Düzenle"
          color="primary"
          icon="edit"
          @click="duzenleDialog = true"
          class="q-px-md"
        />
        <q-btn
          flat
          label="Kapat"
          color="primary"
          @click="kapat"
          class="q-px-md"
        />
        <q-btn
          v-if="personel.telefon"
          flat
          label="Ara"
          color="primary"
          icon="phone"
          :href="'tel:' + personel.telefon"
          class="q-px-md"
        />
      </q-card-actions>
    </q-card>
  </q-dialog>

  <!-- Düzenleme Dialogu -->
  <q-dialog v-model="duzenleDialog">
    <q-card style="min-width: 350px">
      <q-card-section>
        <div class="text-h6">Personel Bilgilerini Düzenle</div>
      </q-card-section>

      <q-card-section class="q-pt-none">
        <q-input
          v-model="duzenlenenPersonel.adi_soyadi"
          label="Adı Soyadı"
          class="q-mb-md"
        />
        <q-input
          v-model="duzenlenenPersonel.unvan_adi"
          label="Ünvan"
          class="q-mb-md"
        />
        <q-input
          v-model="duzenlenenPersonel.birim_adi"
          label="Birim"
          class="q-mb-md"
        />
        <q-input
          v-model="duzenlenenPersonel.telefon"
          label="Telefon"
          class="q-mb-md"
          mask="(###) ### ####"
        />
        <q-input
          v-model="duzenlenenPersonel.email"
          label="E-posta"
          type="email"
          class="q-mb-md"
        />
        <q-input
          v-model="duzenlenenPersonel.dahili"
          label="Dahili"
          class="q-mb-md"
        />
        <q-input
          v-model="duzenlenenPersonel.notlar"
          label="Notlar"
          type="textarea"
          autogrow
        />
      </q-card-section>

      <q-card-actions align="right" class="text-primary">
        <q-btn flat label="İptal" @click="duzenleDialog = false" />
        <q-btn flat label="Kaydet" @click="kaydet" />
      </q-card-actions>
    </q-card>
  </q-dialog>
</template>

<script setup>
import { ref, watch } from 'vue'

const props = defineProps({
  modelValue: {
    type: Boolean,
    default: false
  },
  personel: {
    type: Object,
    default: () => ({
      id: null,
      adi_soyadi: '',
      unvan_adi: '',
      birim_adi: '',
      telefon: '',
      email: '',
      dahili: '',
      notlar: '',
      // API'den gelen alanlar
      unvan: { unvan_adi: '' },
      birim: { birim_adi: '' }
    })
  }
})

const emit = defineEmits(['update:modelValue', 'kaydet'])

// Telefon numarasını formatlama
const formatPhoneNumber = (phone) => {
  if (!phone) return ''
  // Örnek: 5551234567 -> (555) 123 4567
  return phone.replace(/(\d{3})(\d{3})(\d{4})/, '($1) $2 $3')
}

const goster = ref(props.modelValue)
const duzenleDialog = ref(false)
const duzenlenenPersonel = ref({
  id: null,
  adi_soyadi: '',
  unvan_adi: '',
  birim_adi: '',
  telefon: '',
  email: '',
  dahili: '',
  notlar: ''
})

watch(() => props.modelValue, (newVal) => {
  goster.value = newVal
  if (newVal) {
    // Personel verilerini düzenleme formuna kopyala
    duzenlenenPersonel.value = {
      id: props.personel.id,
      adi_soyadi: props.personel.adi_soyadi || '',
      unvan_adi: props.personel.unvan?.unvan_adi || props.personel.unvan_adi || '',
      birim_adi: props.personel.birim?.birim_adi || props.personel.birim_adi || '',
      telefon: props.personel.telefon || '',
      email: props.personel.email || '',
      dahili: props.personel.dahili || '',
      notlar: props.personel.notlar || ''
    }
  }
})

function kapat() {
  goster.value = false
  emit('update:modelValue', false)
}

function kaydet() {
  // API'ye kaydetme işlemi için üst bileşene event gönder
  emit('kaydet', duzenlenenPersonel.value)
  duzenleDialog.value = false
}
</script>
