<script setup lang="ts">
import { ref, watch, onBeforeUnmount } from 'vue';
import Cropper from 'cropperjs';
import 'cropperjs/dist/cropper.css';

const props = defineProps<{
  file: File | null
}>();

const imageRef = ref<HTMLImageElement | null>(null);
const cropper = ref<Cropper | null>(null);
const imageUrl = ref<string>('');

// State tracking for flip
const scaleX = ref(1);
const scaleY = ref(1);

const initCropper = () => {
  if (cropper.value) {
    cropper.value.destroy();
  }

  if (imageRef.value) {
    cropper.value = new Cropper(imageRef.value, {
      viewMode: 1, // Restrict crop box to canvas
      dragMode: 'move', // Default to moving image
      autoCrop: false, // Don't start with crop box
      background: false, // Transparent bg
      responsive: true,
      modal: true,
      guides: true,
      center: true,
      highlight: false,
      cropBoxMovable: true,
      cropBoxResizable: true,
      toggleDragModeOnDblclick: false,
    });
  }
};

watch(() => props.file, (newFile) => {
  if (newFile) {
    // Revoke old object URL to avoid leaks
    if (imageUrl.value) {
      URL.revokeObjectURL(imageUrl.value);
    }
    imageUrl.value = URL.createObjectURL(newFile);
    
    // Wait for image to load in DOM then init cropper
    // Either nextTick or @load on img tag
  }
}, { immediate: true });

const onImageLoad = () => {
  initCropper();
};

// Exposed Methods
const rotate = (deg: number) => {
  cropper.value?.rotate(deg);
};

const flip = (dir: 'x' | 'y') => {
  if (dir === 'x') {
    scaleX.value = scaleX.value * -1;
  } else {
    scaleY.value = scaleY.value * -1;
  }
  cropper.value?.scale(scaleX.value, scaleY.value);
};

const zoom = (delta: number) => {
  cropper.value?.zoom(delta);
};

const toggleCrop = (active: boolean) => {
  if (!cropper.value) return;
  
  if (active) {
    cropper.value.setDragMode('crop');
    cropper.value.crop(); // Show crop box
  } else {
    cropper.value.clear(); // Hide crop box
    cropper.value.setDragMode('move');
  }
};

const getResult = (): string | undefined => {
  if (!cropper.value) return undefined;
  
  // Get cropped canvas
  // We prefer the original resolution
  const canvas = cropper.value.getCroppedCanvas({
    imageSmoothingEnabled: true,
    imageSmoothingQuality: 'high',
  });
  
  return canvas.toDataURL(props.file?.type || 'image/png');
};

defineExpose({
  rotate,
  flip,
  zoom,
  toggleCrop,
  getResult
});

onBeforeUnmount(() => {
  if (cropper.value) {
    cropper.value.destroy();
  }
  if (imageUrl.value) {
    URL.revokeObjectURL(imageUrl.value);
  }
});
</script>

<template>
  <div class="editor-container glass-panel">
    <div class="canvas-wrapper">
      <img 
        ref="imageRef" 
        :src="imageUrl" 
        alt="Editor Image" 
        @load="onImageLoad"
        v-if="imageUrl"
      />
    </div>
  </div>
</template>

<style scoped>
.editor-container {
  flex: 1;
  width: 100%;
  max-width: 1000px;
  margin: 0 auto;
  overflow: hidden;
  position: relative;
  display: flex;
  flex-direction: column;
}

.canvas-wrapper {
  flex: 1;
  height: 100%; /* Important for cropper container */
  position: relative;
  background-color: #000000;
  border-radius: var(--radius-lg);
  overflow: hidden;
}

img {
  max-width: 100%;
  display: block; /* Important for cropper */
}

/* Override some cropper styles for better theme integration if needed */
:deep(.cropper-view-box) {
  outline: 2px solid var(--accent-color);
}
:deep(.cropper-point) {
  background-color: var(--accent-color);
}
</style>
