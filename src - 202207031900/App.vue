<template>
  <v-app>
    <v-main>
      <youTubeFan/>
    </v-main>
  </v-app>
</template>

<script>
import youTubeFan from './components/youTubeFan';

export default {
  name: 'App',

  components: {
    youTubeFan,
  },

  data: () => ({
    //
  }),
};
</script>
