<template>
  <q-page class="row items-center justify-evenly">
    <chat></chat>
  </q-page>
</template>

<script lang="ts">
import { Todo, Meta } from 'components/models';
import Chat from 'components/Chat.vue';
import { defineComponent, ref } from 'vue';

export default defineComponent({
  name: 'IndexPage',
  components: { Chat },
  setup() {
    return { };
  }
});
</script>
