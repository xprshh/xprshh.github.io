<template>
  <div class="scroll-container" ref="scrollContainer">
    <div class="scroll-content">
      <!-- Your scrollable content goes here -->
      <div v-for="i in 100" :key="i">Item {{ i }}</div>
    </div>
    <div class="scrollbar" @mousedown="startDrag">
      <div class="thumb" :style="{ top: thumbTop + 'px', height: thumbHeight + 'px' }"></div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      thumbHeight: 100,
      thumbTop: 0,
      isDragging: false,
      startY: 0,
      startTop: 0,
    };
  },
  methods: {
    updateThumb() {
      const container = this.$refs.scrollContainer;
      const content = container.querySelector('.scroll-content');
      const containerHeight = container.clientHeight;
      const contentHeight = content.scrollHeight;
      
      if (contentHeight <= containerHeight) {
        this.thumbHeight = 0;
        this.thumbTop = 0;
        return;
      }
      
      this.thumbHeight = (containerHeight / contentHeight) * containerHeight;
      this.thumbTop = (container.scrollTop / contentHeight) * containerHeight;
    },
    startDrag(event) {
      this.isDragging = true;
      this.startY = event.clientY;
      this.startTop = this.thumbTop;
      window.addEventListener('mousemove', this.onDrag);
      window.addEventListener('mouseup', this.stopDrag);
    },
    onDrag(event) {
      if (!this.isDragging) return;
      const deltaY = event.clientY - this.startY;
      this.thumbTop = Math.min(
        Math.max(this.startTop + deltaY, 0),
        this.$refs.scrollContainer.clientHeight - this.thumbHeight
      );
      const container = this.$refs.scrollContainer;
      const contentHeight = container.querySelector('.scroll-content').scrollHeight;
      container.scrollTop = (this.thumbTop / container.clientHeight) * contentHeight;
    },
    stopDrag() {
      this.isDragging = false;
      window.removeEventListener('mousemove', this.onDrag);
      window.removeEventListener('mouseup', this.stopDrag);
    },
  },
  mounted() {
    this.updateThumb();
    window.addEventListener('resize', this.updateThumb);
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.updateThumb);
  },
  watch: {
    '$refs.scrollContainer.scrollTop': 'updateThumb',
  },
};
</script>

<style scoped>
.scroll-container {
  position: relative;
  width: 300px; /* Set the width as needed */
  height: 400px; /* Set the height as needed */
  overflow: hidden;
  border: 1px solid #ccc;
}

.scroll-content {
  overflow: auto;
  height: 100%;
  width: 100%;
}

.scrollbar {
  position: absolute;
  right: 0;
  top: 0;
  width: 10px;
  height: 100%;
  background: #f0f0f0;
}

.thumb {
  position: absolute;
  width: 100%;
  background: #888;
  cursor: pointer;
}
</style>
