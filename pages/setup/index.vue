<template>
  <v-row justify="center" align="center">
    <v-col cols="12" sm="8" md="6">
      <div class="text-center">

      </div>
      <v-card>
        <v-card-title class="headline">
          {{$t('welcome')}} to the {{$t('hr_system')}}
        </v-card-title>


      </v-card>
    </v-col>
  </v-row>

</template>

<script>
export default {
  name: "index.vue",
  middleware: ["auth"],
  created() {
    this.$store.commit('SHOW_LOADER',false)
  }
}
</script>

<style scoped>

</style>
