<template>
  <article>
    <md-list class="md-triple-line">
      <md-list-item>
        <md-avatar>
          <img src="https://image.flaticon.com/icons/svg/147/147144.svg" alt="avatar">
        </md-avatar>

        <div class="md-list-item-text">
          <span>Utilisateur anonyme</span>
          <div>
            <icon-star v-for="i in review.stars" :key="i"></icon-star>
          </div>
          <p>{{ review.comment }}</p>
        </div>
      </md-list-item>
    </md-list>
  </article>
</template>

<script>
  import IconStar from './icons/IconStar.vue';

  export default {
    name: 'Review',
    components: {
      IconStar
    },
    props: {
      review: {
        type: Object,
        required: true
      }
    }
  }
</script>

<style scoped lang="scss">
  .md-list-item-text * {
    width: auto !important;
  }
</style>
