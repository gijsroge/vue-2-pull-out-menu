<script>
import { defineComponent } from 'vue'

export default defineComponent({
  name: 'App',
  data() {
    return {
      isDragging: false,
      startYPosition: 0,
      y: 0,
      isOpen: false,
      treshHold: 50,
      height: 500,
    }
  },
  computed: {
    style: function () {
      return {
        transform: `translateY(-${this.y}px)`,
      }
    },
  },
  watch: {
    isOpen: function () {
      if (this.isOpen) this.startYPosition = this.height
      else this.startYPosition = 0
    },
    y: function () {
      if (!this.isDragging) {
        this.isOpen = this.y > this.treshHold
      }
    },
  },
  methods: {
    toggle() {
      this.y = this.y === this.height ? 0 : this.height
    },
    clamp(num, min, max) {
      return Math.min(Math.max(num, min), max)
    },
    startDrag(e) {
      this.startYPosition = (e.clientY || e.touches[0].clientY) + (this.isOpen ? this.height : 0)
      this.isDragging = true
    },
    stopDrag() {
      if (!this.isDragging) return
      if (this.isOpen) {
        if (this.y < this.height - this.treshHold) {
          this.y = 0
        } else {
          this.y = this.height
        }
      } else {
        if (this.y >= this.treshHold) {
          this.y = this.height
        } else {
          this.y = 0
        }
      }

      this.startYPosition = this.y
      this.isDragging = false
    },
    whileDrag(e) {
      if (!this.isDragging) return

      const clientY = e.clientY || e.touches[0].clientY
      this.y = this.clamp(this.startYPosition - clientY, 0, this.height)
    },
  },
})
</script>

<template>
  <div class="overflow-hidden" @mouseup="stopDrag" @touchend="stopDrag" @touchmove="whileDrag" @mousemove="whileDrag">
    <div class="h-screen p-3">
      isDragging: {{ isDragging }} <br />
      startYPosition: {{ startYPosition }} <br />
      y: {{ y }} <br />
      isOpen: {{ isOpen }} <br />

      <button class="bg-black text-white rounded px-2 py-1" @click="toggle">toggle</button>
    </div>
    <div
      v-bind:style="style"
      class="fixed bottom-0 w-full left-0"
      :class="[{ 'transition-transform': !isDragging }, { 'ease-linear': isDragging }]"
    >
      <div
        @mousedown="startDrag"
        @touchstart="startDrag"
        class="
          cursor-move
          select-none
          drag-handle
          h-[50px]
          bg-black
          w-full
          text-white
          flex
          items-center
          text-center
          justify-center
        "
      >
        --------
      </div>
    </div>
  </div>
</template>

<style></style>
