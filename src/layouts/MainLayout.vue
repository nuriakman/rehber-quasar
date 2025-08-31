<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          icon="menu"
          aria-label="Menu"
          @click="toggleLeftDrawer"
        />

        <q-toolbar-title> Quasar App </q-toolbar-title>

        <div>Quasar v{{ $q.version }}</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
    >
      <q-list>
        <q-item-label header> Ana Sayfa </q-item-label>
        <q-item
          clickable
          tag="router-link"
          :to="{ path: '/' }"
        >
          <q-item-section avatar>
            <q-icon name="home" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Ana Sayfa</q-item-label>
            <q-item-label caption>Ana sayfayı görüntüle</q-item-label>
          </q-item-section>
        </q-item>
        <q-item-label header> Yönetim Sayfaları </q-item-label>
        <q-item
          clickable
          tag="router-link"
          :to="{ path: '/birimler' }"
        >
          <q-item-section avatar>
            <q-icon name="work" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Birimler</q-item-label>
            <q-item-label caption>Birim listesini görüntüle</q-item-label>
          </q-item-section>
        </q-item>
        <q-item
          clickable
          tag="router-link"
          :to="{ path: '/unvanlar' }"
        >
          <q-item-section avatar>
            <q-icon name="badge" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Unvanlar</q-item-label>
            <q-item-label caption>Unvan listesini görüntüle</q-item-label>
          </q-item-section>
        </q-item>
        <q-item
          clickable
          tag="router-link"
          :to="{ path: '/personeller' }"
        >
          <q-item-section avatar>
            <q-icon name="groups" />
          </q-item-section>
          <q-item-section>
            <q-item-label>Personeller</q-item-label>
            <q-item-label caption>Personel listesini görüntüle</q-item-label>
          </q-item-section>
        </q-item>
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script setup>
import { ref } from 'vue'
const leftDrawerOpen = ref(false)

function toggleLeftDrawer() {
  leftDrawerOpen.value = !leftDrawerOpen.value
}
</script>
