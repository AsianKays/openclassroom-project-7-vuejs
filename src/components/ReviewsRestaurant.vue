<template>
  <md-dialog id="dialog" :md-active.sync="showDialog" @md-closed="closeDialog">
    <md-dialog-title>Tous les avis sur « {{ restaurantName }} » </md-dialog-title>
    <md-divider></md-divider>

    <md-chip class="md-primary chips" md-clickable @click="resetInputs()">
      <md-icon>rate_review</md-icon>
      Rédiger un avis
    </md-chip>

    <transition name="slide-fade">
      <div class="form" v-if="showTextarea">
        <div style="display: flex;" class="clickable">
          <div v-for="i in rate" :key="i" @click="rate = i">
            <icon-star format="full" ></icon-star>
          </div>

          <div v-for="i in (5-rate)" :key="`${i}-empty`" @click="rate += i">
            <icon-star format="empty"></icon-star>
          </div>
        </div>
        <md-field>
          <label>Qu'avez-vous penser de ce restaurant ?</label>
          <md-textarea v-model="reviewContent" md-counter="500"></md-textarea>
        </md-field>
        <md-button class="md-raised md-primary button-textarea" id="button-send" @click="addReview()">Donner mon avis</md-button>
        <md-button class="md-primary button-textarea" @click="resetInputs()">Annuler</md-button>
      </div>
    </transition>

    <Review v-for="(review, index) in reviews" :key="index" :review="review"></Review>

    <md-dialog-actions>
      <md-button class="md-primary" @click="closeDialog">Fermer</md-button>
    </md-dialog-actions>
  </md-dialog>
</template>

<script>
  import IconStar from './icons/IconStar.vue';
  import Review from './Review.vue';
  import {eventBus} from "../main";

  export default {
    name: 'ReviewsRestaurant',
    components: {
      IconStar,
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
      rate: 1,
      reviewContent: null
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
        this.showDialog = false;
        this.$emit('closed', false);
      },

      addReview() {
        const review = {
          stars: this.rate,
          comment: this.reviewContent
        };
        eventBus.$emit('newReview' + this.restaurantName, review);
        this.resetInputs();
      },

      resetInputs() {
        this.stars = 1;
        this.reviewContent = '';
        this.showTextarea = !this.showTextarea;
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

  .form {
    width: 95%;
    margin: 20px auto 10px;
  }

  .button-textarea {
    float: right;
  }

  #button-send {
    color: #fff !important;
  }

  .slide-fade-enter-active {
    transition: all .3s ease;
  }

  .slide-fade-leave-active {
    transition: all .3s cubic-bezier(1.0, 0.5, 0.8, 1.0);
  }

  .slide-fade-enter, .slide-fade-leave-to {
    transform: translateX(10px);
    opacity: 0;
  }
</style>
