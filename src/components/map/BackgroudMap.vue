<template>
  <yandex-map v-bind="ymapSettings">
    <ymap-marker
      v-for="(coords, index) in ymapMarkerCoords"
      :key="index"
      :coords="coords"
      :marker-id="index"
      :icon="markerIcon"
    />
  </yandex-map>
</template>

<script>
import { yandexMap, ymapMarker } from 'vue-yandex-maps'

export default {
  name: 'BackgroudMap',
  components: { yandexMap, ymapMarker },

  data() {
    return {
      ymapSettings: {
        coords: [55.75, 37.62],
        zoom: 12,
        controls: []
      },

      ymapMarkerCoords: Object.keys([...Array(30)]).map(() => [
        55.7 + Math.random() * 0.1,
        37.57 + Math.random() * 0.1
      ]),

      markerIcon: {
        layout: 'default#imageWithContent',
        contentLayout:
          '<div class="ymap-balloon"><div class="ymap-balloon__inner"></div></div>'
      }
    }
  }
}
</script>

<style lang="scss">
.ymap-container {
  width: 100vw;
  height: 100vh;
}

.ymap-balloon {
  position: relative;
  border-radius: 50%;
  padding: 8px;
  width: 32px;
  height: 32px;
  background: $blue;

  &::after {
    content: '';
    position: absolute;
    left: 15px;
    bottom: 0;
    z-index: 1;
    width: 8px;
    height: 8px;
    background: $blue;
    transform: rotate(45deg) translateY(50%);
  }

  &__inner {
    position: relative;
    z-index: 2;
    border-radius: 50%;
    width: 16px;
    height: 16px;
    background: $white;
  }
}
</style>
