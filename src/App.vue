<template>
  <div id="app">
    <backgroud-map />
    <sidebar-body />
  </div>
</template>

<script>
import BackgroudMap from '@/components/map/BackgroudMap'
import SidebarBody from '@/components/footer-sidebar/SidebarBody'

export default {
  name: 'App',
  components: {
    BackgroudMap,
    SidebarBody
  }
}
</script>
