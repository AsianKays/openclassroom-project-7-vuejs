<template>
  <md-dialog id="dialog" :md-active.sync="showDialog" @md-closed="closeDialog">
    <md-dialog-title>Tous les avis sur « {{ restaurantName }} » </md-dialog-title>
    <md-divider></md-divider>
    <md-chip class="md-primary chips" md-clickable @click="showTextarea = !showTextarea">
      <md-icon>rate_review</md-icon>
      Rédiger un avis
    </md-chip>

    <transition name="slide-fade">
      <div v-if="showTextarea">
        <md-field class="textarea">
          <label>Qu'avez vous penser de ce restaurant ?</label>
          <md-textarea v-model="textarea" md-counter="500"></md-textarea>
        </md-field>
        <md-button class="md-raised md-primary button-textarea" id="button-send">Donner mon avis</md-button>
        <md-button class="md-primary button-textarea" @click="showTextarea = !showTextarea">Annuler</md-button>
      </div>
    </transition>

    <Review v-for="(review, index) in reviews" :key="index" :review="review"></Review>

    <md-dialog-actions>
      <md-button class="md-primary" @click="closeDialog">Fermer</md-button>
    </md-dialog-actions>
  </md-dialog>
</template>

<script>
  import Review from './Review.vue';

  export default {
    name: "test",
    components: {
      Review
    },
    props: {
      restaurantName: {
        type: String,
        required: true
      },
      state: {
        type: Boolean,
        required: true
      },
      reviews: {
        type: Array,
        required: true
      }
    },
    data: () => ({
      showDialog: false,
      showTextarea: false,
      textarea: null
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
      }
    }
  }
</script>

<style scoped lang="scss">
  #dialog {
    overflow-y: auto;
  }

  .chips{
    border: 1px solid #fff;
    color: #fff !important;
    width: fit-content;
    margin: 16px 0 0 16px;
    &:hover {
      color: #42b883 !important;
      .md-icon{
        color: #42b883 !important;
      }
    }
  }

  .textarea {
    width: 95%;
    margin: 20px auto 10px;
  }

  .button-textarea {
    float: right;
  }

  #button-send {
    color: #fff !important;
  }

  /* Les animations d'entrée (« enter ») et de sortie (« leave »)  */
  /* peuvent utiliser différentes fonctions de durée et de temps.  */
  .slide-fade-enter-active {
    transition: all .3s ease;
  }
  .slide-fade-leave-active {
    transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0);
  }
  .slide-fade-enter, .slide-fade-leave-to
    /* .slide-fade-leave-active below version 2.1.8 */ {
    transform: translateX(10px);
    opacity: 0;
  }
</style>
