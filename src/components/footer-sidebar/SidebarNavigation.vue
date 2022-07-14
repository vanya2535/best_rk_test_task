<template>
  <nav class="sidebar-navigation">
    <button
      v-for="section of sections"
      :key="section.value"
      class="sidebar-navigation__item"
      :class="{ 'sidebar-navigation__item_selected': section.value === value }"
      @click="$emit('input', section.value)"
    >
      {{ section.label }}
    </button>
    <div
      class="sidebar-navigation__underline"
      :style="`left: ${value * 27}vw`"
    ></div>
  </nav>
</template>

<script>
export default {
  name: 'SidebarNavigation',

  props: {
    value: {
      type: Number,
      required: true,
      validator: (value) => value >= 0
    },

    sections: {
      type: Array,
      required: true,

      validator: (sections) =>
        sections.every((section) =>
          ['label', 'value'].every((field) => section.hasOwnProperty(field))
        )
    }
  }
}
</script>

<style lang="scss" scoped>
.sidebar-navigation {
  position: relative;
  z-index: 1;
  display: grid;
  grid-template-columns: repeat(5, 27vw);
  overflow: scroll;
  margin-top: 10px;
  border-radius: 5px;
  height: 40px;
  max-width: calc(100vw - 12px);
  background: rgba($color: $white, $alpha: 0.2);

  &::-webkit-scrollbar {
    display: none;
  }

  &__item {
    font-weight: 500;
    font-size: 13px;
    color: rgba($color: $white, $alpha: 0.7);

    &_selected {
      color: $white;
    }
  }

  &__underline {
    position: absolute;
    bottom: 2px;
    z-index: 2;
    border-radius: 4px;
    width: 27vw;
    height: 2px;
    background: rgba($color: $white, $alpha: 0.7);
    transition: 0.3s;
  }
}
</style>
