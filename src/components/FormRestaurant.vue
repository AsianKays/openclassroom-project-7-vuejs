<template>
  <div>
    <md-dialog :md-active.sync="showDialog" @md-closed="closeDialog">
      <md-dialog-title>Ajouter un restaurant</md-dialog-title>
      <md-divider></md-divider>

      <md-field>
        <label>Nom du restaurant</label>
        <md-input v-model="restaurantName"></md-input>
      </md-field>

      <md-field>
        <label>Adresse</label>
        <md-input v-model="address" readonly></md-input>
      </md-field>
    </md-dialog>
  </div>
</template>

<script>
  export default {
    name: 'FormRestaurant',
    props: {
      restaurantName: {
        type: String,
        required: true
      },
      address: {
        type: String,
        required: true
      },
      state: {
        type: Boolean,
        required: true
      }
    },
    data: () => ({
      showDialog: false
    }),
    watch: {
      state: {
        immediate: true,
        handler() {
          this.showDialog = this.state;
        }
      }
    },
    methods: {
      /**
       * Triggered when the component is closed.
       * It will call the parent component and update the current state.
       */
      closeDialog() {
        this.showDialog= false;
        this.$emit('closed', false);
      },
    }
  }
</script>

<style scoped lang="scss">

</style>
