<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn flat dense round icon="menu" aria-label="Menu" @click="toggleLeftDrawer" />

        <q-toolbar-title>
          Gemini App
        </q-toolbar-title>

        <div>By Luis Chang v0.1</div>
      </q-toolbar>
    </q-header>

    <q-drawer v-model="leftDrawerOpen" bordered>
      <q-list>
        <q-item-label header>
          Menu
        </q-item-label>

        <EssentialLink v-for="link in essentialLinks" :key="link.title" v-bind="link" @click="toggleLeftDrawer(link)" />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import { defineComponent, ref } from 'vue'
import EssentialLink from 'components/EssentialLink.vue'
import { useRouter } from 'vue-router' // Importar useRouter de vue-router


const linksList = [

  {
    title: 'Chat con Gemini',
    caption: 'gemini app',
    icon: 'chat',
    link: '/#/chat'
  }
]

export default defineComponent({
  name: 'MainLayout',

  components: {
    EssentialLink
  },

  setup() {
    const leftDrawerOpen = ref(false)
    const router = useRouter() // Obtener el router


    return {
      essentialLinks: linksList,
      leftDrawerOpen,
      toggleLeftDrawer(link) {
        leftDrawerOpen.value = !leftDrawerOpen.value
        if (link) {
          console.log("link... " + link)
          router.push(link);
        }
      }
    }
  }
})
</script>
