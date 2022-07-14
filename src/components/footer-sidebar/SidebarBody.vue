<template>
  <div
    class="sidebar-body"
    :class="`sidebar-body${position}`"
    :style="sidebarBodyStyle"
    @touchstart="onTouchStart"
    @touchmove="onTouchMove"
    @touchend="onTouchEnd"
  >
    <div class="sidebar-body__handle" />

    <div
      class="sidebar-body__content"
      @touchstart.stop
      @touchmove.stop
      @touchend.stop
    >
      <filter-input v-model="filterValue" @focus="position = '_top'" />
      <sidebar-navigation v-model="currentSection" :sections="navbarSections" />

      <div class="sidebar-body__cards" :style="sidebarCardsStyle">
        <div class="sidebar-body__edge" index="0" />

        <sidebar-card
          v-for="(card, index) of visibleCards"
          :key="index"
          :card="card"
        />

        <div class="sidebar-body__edge" index="1" />
      </div>
    </div>
  </div>
</template>

<script>
import FilterInput from './FilterInput'
import SidebarNavigation from './SidebarNavigation'
import SidebarCard from './SidebarCard'

export default {
  name: 'SidebarBody',
  components: { FilterInput, SidebarNavigation, SidebarCard },

  data() {
    return {
      touchMoving: false,
      sidebarPositionTop: 0,
      firstTouchPosition: 0,
      position: '',

      filterValue: '',
      currentSection: 0,

      navbarSections: [
        {
          label: 'Все',
          value: 0
        },
        {
          label: 'Места',
          value: 1
        },
        {
          label: 'Скидки',
          value: 2
        },
        {
          label: 'Карты',
          value: 3
        },
        {
          label: 'Продукты',
          value: 4
        }
      ],

      cards: [
        [
          {
            icon: 'place',
            title: 'Москва Места',
            description: 'Московская местная москва'
          },
          {
            icon: 'place',
            title: 'Москва Места',
            description: 'Московская местная москва'
          },
          {
            icon: 'place',
            title: 'Москва Места',
            description: 'Московская местная москва'
          }
        ],

        [
          {
            icon: 'discount',
            title: 'Москва Скидки',
            description: 'Московская скидочная москва'
          },
          {
            icon: 'discount',
            title: 'Москва Скидки',
            description: 'Московская скидочная москва'
          },
          {
            icon: 'discount',
            title: 'Москва Скидки',
            description: 'Московская скидочная москва'
          }
        ],

        [
          {
            icon: 'map',
            title: 'Москва Карты',
            description: 'Московская карточная москва'
          },
          {
            icon: 'map',
            title: 'Москва Карты',
            description: 'Московская карточная москва'
          },
          {
            icon: 'map',
            title: 'Москва Карты',
            description: 'Московская карточная москва'
          }
        ],

        [
          {
            icon: 'products',
            title: 'Москва Продукты',
            description: 'Московская продуктовая москва'
          },
          {
            icon: 'products',
            title: 'Москва Продукты',
            description: 'Московская продуктовая москва'
          },
          {
            icon: 'products',
            title: 'Москва Продукты',
            description: 'Московская продуктовая москва'
          }
        ]
      ]
    }
  },

  computed: {
    sidebarBodyStyle() {
      // Пока пользователь двигает сайдбар, транзишн не нужен
      if (this.touchMoving) {
        return `bottom: calc(100vh - ${this.sidebarPositionTop}px)`
      } else {
        return 'transition: 0.1s;'
      }
    },

    sidebarCardsStyle() {
      // Это свойство нужно для правильного отображения карточек без эффекта обрезания
      if (this.touchMoving) {
        return 'max-height: 100vh'
      } else {
        return ''
      }
    },

    visibleCards() {
      // Регулярка для поиска без учета регистра
      const regexp = new RegExp(this.filterValue, 'gi')

      if (this.currentSection === 0) {
        return this.cards
          .reduce((acc, arr) => [...acc, ...[...arr, ...arr, ...arr, ...arr]]) // Оператор расширения использован для большого количества карточек без захламления даты, т.к. этот проект - игрушечный
          .filter(
            ({ title, description }) =>
              title.match(regexp) || description.match(regexp)
          )
      } else {
        const cards = this.cards[this.currentSection - 1]

        return [...cards, ...cards, ...cards, ...cards].filter(
          ({ title, description }) =>
            title.match(regexp) || description.match(regexp)
        )
      }
    }
  },

  methods: {
    onTouchStart(e) {
      this.firstTouchPosition = e.touches[0].clientY
      this.sidebarPositionTop = e.touches[0].clientY
      this.touchMoving = true
    },

    onTouchEnd(e) {
      this.touchMoving = false
      const touchPosition = e.changedTouches[0].clientY
      const midPosition = 0.5 * document.body.clientHeight

      // Определяем направление движения пальца и в зависимости от него выбираем позицию
      if (this.firstTouchPosition > touchPosition) {
        if (touchPosition < midPosition) {
          this.position = '_top'
        } else {
          this.position = '_mid'
        }
      } else {
        if (touchPosition < midPosition) {
          this.position = '_mid'
        } else {
          this.position = ''
        }
      }
    },

    onTouchMove(e) {
      this.sidebarPositionTop =
        e.targetTouches[0].clientY > 0
          ? e.targetTouches[0].clientY
          : this.sidebarPositionTop
    },

    initObserver() {
      // Функция написана для использования ее в коллбэке обсервера, так как он имеет контекст самого обсервера
      const setPosition = (position) => {
        this.position = position
      }

      // При первой прокрутке первый край будет засчитан вхожящим, что нам не нужно, поэтому добавим переменную для проверки
      let firstIntersect = false

      const options = {
        root: document.querySelector('.sidebar-body__cards'),
        rootMargin: '0px',
        threshold: 1.0
      }

      const callback = function (entries) {
        for (let entry of entries) {
          if (entry.isIntersecting) {
            if (entry.target.attributes.index.value !== '0') {
              setPosition('_top')
            } else {
              if (firstIntersect) {
                setPosition('_mid')
              } else {
                firstIntersect = true
              }
            }
          }
        }
      }

      const observer = new IntersectionObserver(callback, options)

      observer.observe(
        document.querySelector('.sidebar-body__edge:first-child')
      )
      observer.observe(document.querySelector('.sidebar-body__edge:last-child'))
    }
  },

  mounted() {
    this.initObserver()
  }
}
</script>

<style lang="scss" scoped>
.sidebar-body {
  position: absolute;
  left: 0;
  right: 0;
  bottom: 70px;
  z-index: 3;
  min-height: 100vh;
  background: $dark;
  transform: translateY(100%);

  &_mid {
    bottom: 50vh;

    .sidebar-body__cards {
      max-height: calc(50vh - 122px);
    }
  }

  &_top {
    bottom: 90vh;

    .sidebar-body__cards {
      max-height: calc(90vh - 122px);
    }
  }

  &__handle {
    position: relative;
    z-index: 1;
    height: 24px;

    &::after {
      content: '';
      position: absolute;
      left: 50%;
      top: 50%;
      z-index: 2;
      border-radius: 10px;
      width: 30vw;
      height: 6px;
      background: rgba($color: $white, $alpha: 0.7);
      transform: translate(-50%, -50%);
    }
  }

  &__content {
    padding: 0 12px;
  }

  &__cards {
    display: grid;
    overflow-y: scroll;
    margin-top: 12px;
    padding-bottom: 10px;
    max-height: calc(90vh - 122px);
    row-gap: 4px;

    &::-webkit-scrollbar {
      display: none;
    }
  }
}
</style>
