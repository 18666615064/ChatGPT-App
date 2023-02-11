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

        <q-toolbar-title>
          ChatGPT
        </q-toolbar-title>

      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      show-if-above
      bordered
    >
      <q-list>
        <q-item-label
          header
        >
        Settings
        </q-item-label>

        <EssentialLink
          v-for="link in linksList"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script lang="ts">
import { defineComponent, ref } from 'vue';
import EssentialLink from 'components/EssentialLink.vue';
import { useQuasar } from 'quasar'
import { inject } from 'vue'
let bus:any = null;
export default defineComponent({
  name: 'MainLayout',

  components: {
    EssentialLink
  },

  data() {
    return {
      key: "x",
      $bus: Object,
      linksList: [
        {
          title: 'ApiKey',
          caption: 'Set your api from OpenAI',
          icon: 'key',
          onClick: () => {
            this.$q.dialog({
              title: 'ApiKey',
              message: 'Input your ApiKey',
              prompt: {
                model: '' + this.key,
                type: 'text' // optional
              },
              cancel: true,
              persistent: true
            }).onOk(data => {
              this.key = data;
              localStorage.setItem('key', data);
              bus.emit('keychange', data);
            })
          }
        },
      ]
    }
  },
  created() {
    this.key = localStorage.getItem("key") || "";
    this.$q = useQuasar()
    bus = inject('bus')
  },
  setup () {
    const leftDrawerOpen = ref(false)
    
    return {
      leftDrawerOpen,
      toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      }
    }
  }
});
</script>
