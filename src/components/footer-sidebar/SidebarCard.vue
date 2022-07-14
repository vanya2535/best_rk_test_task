<template>
  <div class="card">
    <img
      :src="require(`@/assets/icons/${card.icon}.svg`)"
      :alt="card.title"
      class="card__image"
    />

    <div class="card__body">
      <div class="card__title">{{ card.title }}</div>
      <div class="card__description" v-html="card.description"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SidebarCard',

  props: {
    card: {
      type: Object,
      required: true,

      validator: (card) =>
        ['icon', 'title', 'description'].every((field) =>
          card.hasOwnProperty(field)
        )
    }
  }
}
</script>

<style lang="scss" scoped>
.card {
  display: grid;
  column-gap: 12px;
  align-items: center;
  grid-template-columns: 48px 1fr;
  border-radius: 4px;
  padding: 8px;
  height: 64px;
  background: rgba($color: $white, $alpha: 0.9);

  &__image {
    width: 48px;
    height: 48px;
    object-fit: contain;
  }

  &__title {
    font-weight: 500;
    font-size: 13px;
    color: $dark;
  }

  &__description {
    font-weight: 400;
    font-size: 12px;
    color: rgba($color: $dark, $alpha: 0.8);
  }
}
</style>
