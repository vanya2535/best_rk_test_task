<template>
  <div class="filter-input__wrapper">
    <input
      v-model="modelValue"
      placeholder="Поиск мест, скидок, карт поблизости"
      type="text"
      class="filter-input"
      @focus="$emit('focus')"
    />

    <img
      class="filter-input__icon"
      src="@/assets/icons/search.svg"
      alt="Поиск"
    />
  </div>
</template>

<script>
export default {
  name: 'FilterInput',

  props: {
    value: {
      type: [String, Number],
      required: true
    }
  },

  computed: {
    modelValue: {
      get() {
        return this.value
      },

      set(value) {
        this.$emit('input', value)
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.filter-input {
  position: absolute;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  z-index: 2;
  border: none;
  border-radius: 8px;
  padding: 0 24px 0 16px;
  font-size: 12px;
  color: $white;
  background: $gray;

  &::placeholder {
    color: rgba($color: $white, $alpha: 0.5);
  }

  &__wrapper {
    position: relative;
    z-index: 1;
    width: 100%;
    height: 36px;
  }

  &__icon {
    position: absolute;
    right: 10px;
    top: 50%;
    z-index: 3;
    width: 20px;
    height: 20px;
    transform: translateY(-50%);
  }
}
</style>
