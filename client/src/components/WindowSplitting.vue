<template>
  <div class="main-wrapper" v-on:mousemove="onMouseMoveOnWrapper" v-on:mouseup="onMouseUpOnWrapper">
    <div class="main-side" :style="{paddingRight: `${displayedWidth + 10}px`}">
      <slot name="main"/>
    </div>
    <div
      class="border"
      v-bind:style="{ right: `${displayedWidth + 10}px` }"
      v-on:mousedown="onMouseDownOnBorder"
      v-on:mouseup.prevent="onMouseUpOnBorder"
    />
    <div ref="side" class="expand-side" v-bind:style="{ width: `${displayedWidth}px` }">
      <slot name="side"/>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      originalSideWidth: null,
      sideWidth: null,
      resizing: false,
      originalPageX: null
    }
  },
  computed: {
    configSideWidth() {
      const config = this.$store.state.app.config
      if (config.rightMenu && config.rightMenu.width) {
        this.sideWidth = config.rightMenu.width
        return config.rightMenu.width
      }
      return 250
    },
    displayedWidth() {
      return this.sideWidth || this.configSideWidth
    }
  },
  methods: {
    onMouseUpOnWrapper (e) {
      this.resizing = false
    },
    onMouseMoveOnWrapper (e) {
      if (!this.resizing) return
      const difference = this.originalPageX - e.pageX
      this.sideWidth = this.originalSideWidth + difference
      if (this.sideWidth < 250) this.sideWidth = 250
      if (this.sideWidth > 1000) this.sideWidth = 1000
      e.preventDefault()
    },
    onMouseDownOnBorder (e) {
      this.resizing = true
      this.originalPageX = e.pageX
      this.originalSideWidth = this.sideWidth
    },
    onMouseUpOnBorder (e) {
      this.resizing = false
    },
    expand () {
      this.sideWidth = 800
    },
    shrink () {
      this.sideWidth = 250
    }
  }
}
</script>


<style lang="scss" scoped>
.main-wrapper {
  position: relative;
  width: 100%;
  min-height: 100%;
  .main-side {
    min-height: 100%;
  }
  .expand-side {
    position: fixed;
    top: 0px;
    right: 0px;
    background: #fff;
  }
  .border {
    height: 100%;
    position: absolute;
    width: 10px;
    background: silver;
    top: 0px;
    cursor: ew-resize;
  }
}
</style>
