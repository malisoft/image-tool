<script setup lang="ts">
import { ref } from 'vue';

const emit = defineEmits<{
  (e: 'file-selected', file: File): void
}>();

const isDragging = ref(false);
const fileInput = ref<HTMLInputElement | null>(null);

const handleDragOver = (e: DragEvent) => {
  e.preventDefault();
  isDragging.value = true;
};

const handleDragLeave = (e: DragEvent) => {
  e.preventDefault();
  isDragging.value = false;
};

const handleDrop = (e: DragEvent) => {
  e.preventDefault();
  isDragging.value = false;
  
  if (e.dataTransfer?.files && e.dataTransfer.files.length > 0) {
    const file = e.dataTransfer.files[0];
    if (file) validateAndEmit(file);
  }
};

const handleFileChange = (e: Event) => {
  const target = e.target as HTMLInputElement;
  if (target.files && target.files.length > 0) {
    const file = target.files[0];
    if (file) validateAndEmit(file);
  }
};

const validateAndEmit = (file: File) => {
  if (file.type.startsWith('image/')) {
    emit('file-selected', file);
  } else {
    alert('Please upload an image file');
  }
};

const triggerFileInput = () => {
  fileInput.value?.click();
};
</script>

<template>
  <div 
    class="uploader-container glass-panel"
    :class="{ 'dragging': isDragging }"
    @dragover="handleDragOver"
    @dragleave="handleDragLeave"
    @drop="handleDrop"
    @click="triggerFileInput"
  >
    <div class="content">
      <div class="icon-wrapper">
        <i class="ph ph-upload-simple"></i>
      </div>
      <h2>Upload an Image</h2>
      <p>Drag & drop or click to browse</p>
      <input 
        type="file" 
        ref="fileInput" 
        @change="handleFileChange" 
        accept="image/*" 
        hidden 
      />
    </div>
  </div>
</template>

<style scoped>
.uploader-container {
  width: 100%;
  max-width: 600px;
  height: 400px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin: auto;
  cursor: pointer;
  transition: all 0.3s ease;
}

.uploader-container:hover, .uploader-container.dragging {
  border-color: var(--accent-color);
  background: rgba(59, 130, 246, 0.1);
}

.content {
  text-align: center;
  color: var(--text-secondary);
}

.icon-wrapper {
  font-size: 48px;
  margin-bottom: 16px;
  color: var(--accent-color);
}

h2 {
  color: var(--text-primary);
  margin-bottom: 8px;
}
</style>
