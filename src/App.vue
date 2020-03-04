<template>
  <div>
    <vue-particles
      color="#ffcbea"
      :particleOpacity="0.7"
      :particlesNumber="120"
      shapeType="polygon"
      :particleSize="5"
      linesColor="#ffcbea"
      :linesWidth="2"
      :lineLinked="true"
      :lineOpacity="0.4"
      :linesDistance="150"
      :moveSpeed="3"
      :hoverEffect="true"
      hoverMode="grab"
      :clickEffect="true"
      clickMode="push"
      class="lizi">

    </vue-particles>
    <div style="position: absolute;top: 0;left: 0;min-height: 100%;overflow-y: scroll" id="app" @scroll="scrollEvent">
      <router-view/>
    </div>
  </div>

</template>
<script>
  import bus from './assets/js/bus.js'

  export default {
    methods: {
      scrollEvent (e) {
        if (e.currentTarget.scrollTop + e.currentTarget.clientHeight >= e.currentTarget.scrollHeight) {
          // this.parmasList.pageNum = this.parmasList.pageNum + 1
          // this.send(this.parmasList)
          // alert('a')
          bus.$emit('end', true)
        } else {
          bus.$emit('end', false)
        }
      }
    }
  }
</script>
<style lang="scss">
  #particles-js {
    width: 100%;
    height: 100%;
    position: absolute;
  }

  .lizi {
    background-size: 100%;

  }

  .test {
    background: #ffcbea;
  }

  @import "./assets/css/main.css";
  @import "./assets/css/theme-green/color-green.css";
</style>
