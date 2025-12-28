<script setup lang="ts">
defineProps<{
  isCropMode: boolean
}>();

const emit = defineEmits<{
  (e: 'rotate', degree: number): void,
  (e: 'flip', direction: 'x' | 'y'): void,
  (e: 'toggle-crop'): void,
  (e: 'reset'): void,
  (e: 'download'): void,
  (e: 'zoom', delta: number): void
}>();
</script>

<template>
  <div class="controls-bar glass-panel">
    <div class="group">
      <button class="btn-icon" @click="$emit('zoom', 0.1)" title="Zoom In">
        <i class="ph ph-magnifying-glass-plus"></i>
      </button>
      <button class="btn-icon" @click="$emit('zoom', -0.1)" title="Zoom Out">
        <i class="ph ph-magnifying-glass-minus"></i>
      </button>
    </div>

    <div class="divider"></div>

    <div class="group">
      <button class="btn-icon" @click="$emit('rotate', -90)" title="Rotate Left">
        <i class="ph ph-arrow-counter-clockwise"></i>
      </button>
      <button class="btn-icon" @click="$emit('rotate', 90)" title="Rotate Right">
        <i class="ph ph-arrow-clockwise"></i>
      </button>
    </div>

    <div class="divider"></div>

    <div class="group">
      <button class="btn-icon" @click="$emit('flip', 'x')" title="Flip Horizontal">
        <i class="ph ph-arrows-out-line-horizontal"></i>
      </button>
      <button class="btn-icon" @click="$emit('flip', 'y')" title="Flip Vertical">
        <i class="ph ph-arrows-out-line-vertical"></i>
      </button>
    </div>

    <div class="divider"></div>

    <div class="group">
      <button 
        class="btn-icon" 
        :class="{ active: isCropMode }" 
        @click="$emit('toggle-crop')"
        title="Crop Image"
      >
        <i class="ph ph-crop"></i>
      </button>
    </div>

    <div class="spacer"></div>

    <button class="btn-primary" @click="$emit('download')">
      <i class="ph ph-download-simple"></i>
      Export
    </button>
  </div>
</template>

<style scoped>
.controls-bar {
  display: flex;
  align-items: center;
  padding: 12px 24px;
  gap: 16px;
  margin-top: 20px;
  width: 100%;
  max-width: 800px;
}

.group {
  display: flex;
  gap: 8px;
}

.divider {
  width: 1px;
  height: 24px;
  background: var(--border-color);
}

.spacer {
  flex: 1;
}

i {
  font-size: 20px;
}
</style>
