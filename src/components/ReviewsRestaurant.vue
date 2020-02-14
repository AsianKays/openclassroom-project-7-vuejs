<template>
  <md-dialog id="dialog" :md-active.sync="showDialog" @md-closed="closeDialog">
    <md-dialog-title>Commentaires</md-dialog-title>
    <md-divider></md-divider>
    <md-chip class="md-primary chips" md-clickable>
      <md-icon>rate_review</md-icon>
      Rédiger un avis
    </md-chip>

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
</style>
