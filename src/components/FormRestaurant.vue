<template>
  <div>
    <md-dialog :md-active.sync="showDialog" @md-closed="closeDialog" id="dialog-FormRestaurant">
      <md-dialog-title>
        <md-icon>restaurant_menu</md-icon>
        Ajouter un restaurant
      </md-dialog-title>
      <md-divider></md-divider>

      <md-field :class="errorClass">
        <label>Nom du restaurant</label>
        <md-input v-model="restaurantName" required></md-input>
        <span class="md-error">Ce champ est requis.</span>
      </md-field>

      <md-field>
        <label>Adresse</label>
        <md-input v-model="coords.address" readonly required></md-input>
        <span class="md-error">Ce champ est requis.</span>
      </md-field>

      <md-dialog-actions>
        <md-button class="md-primary" @click="closeDialog">Annuler</md-button>
        <md-button class="md-primary md-raised" id="button-add" @click="addRestaurant">Ajouter</md-button>
      </md-dialog-actions>
    </md-dialog>
  </div>
</template>

<script>
  import {eventBus} from "../main";

  export default {
    name: 'FormRestaurant',
    props: {
      coords: {
        type: Object,
        required: true
      },
      state: {
        type: Boolean,
        required: true
      }
    },
    data: () => ({
      restaurantName: '',
      showDialog: false,
      error: false
    }),
    watch: {
      state: {
        immediate: true,
        handler() {
          this.showDialog = this.state;
        }
      }
    },
    computed: {
      errorClass () {
        return { 'md-invalid': this.error }
      }
    },
    methods: {
      /**
       * Triggered when the cancel button is clicked.
       * It will close the current dialog component.
       */
      closeDialog() {
        this.showDialog = false;
        eventBus.$emit('close-form-restaurant');
      },

      /**
       * Triggered when the component is closed.
       * It will call the parent component, add a new restaurant and update the current state.
       */
      addRestaurant() {
        if (this.restaurantName !== '') {
          const restaurant = {
            restaurantName: this.restaurantName,
            address: this.coords.address,
            lat: this.coords.lat,
            long: this.coords.lng,
            ratings: []
          };
          eventBus.$emit('add-restaurant', restaurant);
          eventBus.$emit('close-form-restaurant');
          this.restaurantName = '';
          this.showDialog = false;
        } else {
          this.error = !this.error;
        }
      },
    }
  }
</script>

<style scoped lang="scss">
  #dialog-FormRestaurant {
    width: 50%;
    padding: 10px 20px;
  }

  #button-add {
    color: #fff !important;
  }
</style>
