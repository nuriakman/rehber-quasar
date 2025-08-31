<template>
  <q-page
    class="row items-center justify-center"
    style="min-height: inherit"
  >
    <!-- Personel Detay Dialog -->
    <PersonelDetay
      v-model="detayGoster"
      :personel="seciliPersonel"
    />

    <div class="col-12 col-md-8">
      <q-card class="q-pa-md q-mt-xl">
        <q-card-section>

          <q-card-title class="text-center">
            <div class="text-h3 text-weight-bold q-mb-sm">Telefon Rehberi</div>

          </q-card-title>
          <div class="text-subtitle1 text-grey-7 q-mb-lg text-center">
            Kurum içi birim, unvan ve personel iletişim bilgilerine hızlı erişim.
          </div>
          <div class="row q-col-gutter-sm">
            <div class="col-9">
              <q-input
                v-model="aramaMetni"
                outlined
                dense
                placeholder="Personel adı veya soyadı giriniz..."
                @keyup.enter="ara"
                :loading="yukleniyor"
              />
            </div>
            <div class="col-3">
              <q-btn
                color="primary"
                label="Ara"
                class="full-width"
                @click="ara"
                :loading="yukleniyor"
              />
            </div>
          </div>
        </q-card-section>


      </q-card>
    </div>

    <div class="col-12 q-px-md">


      <q-card>
        <q-card-section
          v-if="sonuclar.length > 0"
          class="q-pt-none"
        >
          <div class="text-h6 q-mb-md">Arama Sonuçları ({{ sonuclar.length }})</div>
          <div class="row q-col-gutter-md">
            <div
              v-for="personel in sonuclar"
              :key="personel.id"
              class="col-12 col-sm-6 col-md-4"
            >
              <q-card
                class="cursor-pointer"
                @click="personelDetay(personel)"
              >
                <q-card-section class="bg-primary text-white">
                  <div class="text-h6">{{ personel.adi_soyadi }}</div>
                  <div class="text-subtitle2">{{ personel.unvan_adi }}</div>
                </q-card-section>

                <q-separator />

                <q-card-section class="q-pa-sm">
                  <div class="row items-center q-mb-xs">
                    <q-icon
                      name="business"
                      class="q-mr-sm"
                      size="sm"
                    />
                    <span>{{ personel.birim_adi }}</span>
                  </div>
                  <div
                    v-if="personel.telefon"
                    class="row items-center"
                  >
                    <q-icon
                      name="phone"
                      class="q-mr-sm"
                      size="sm"
                    />
                    <span>{{ personel.telefon }}</span>
                  </div>
                </q-card-section>
              </q-card>
            </div>
          </div>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<script setup>
import { ref } from 'vue'
import { api } from 'boot/axios'
import PersonelDetay from 'components/PersonelDetay.vue'

const aramaMetni = ref('')
const sonuclar = ref([])
const yukleniyor = ref(false)
const detayGoster = ref(false)
const seciliPersonel = ref({})

async function ara() {
  // if (!aramaMetni.value.trim()) return


  try {
    yukleniyor.value = true
    const { data } = await api.get('/personel', {
      params: { q: aramaMetni.value }
    })
    sonuclar.value = data
  } catch (error) {
    console.error('Arama sırasında hata oluştu:', error)
    sonuclar.value = []
  } finally {
    yukleniyor.value = false
  }
}

function personelDetay(personel) {
  seciliPersonel.value = { ...personel }
  detayGoster.value = true
}
</script>
