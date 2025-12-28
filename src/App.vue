<script setup lang="ts">
import { ref } from 'vue';
import ImageUploader from './components/ImageUploader.vue';
import ImageEditor from './components/ImageEditor.vue';
import Controls from './components/Controls.vue';

const currentFile = ref<File | null>(null);
const editorRef = ref<InstanceType<typeof ImageEditor> | null>(null);
const isCropMode = ref(false);

const handleFileSelected = (file: File) => {
  currentFile.value = file;
  isCropMode.value = false;
};

const handleRotate = (deg: number) => {
  editorRef.value?.rotate(deg);
};

const handleFlip = (dir: 'x' | 'y') => {
  editorRef.value?.flip(dir);
};

const handleZoom = (delta: number) => {
  editorRef.value?.zoom(delta);
};

const handleToggleCrop = () => {
  isCropMode.value = !isCropMode.value;
  editorRef.value?.toggleCrop(isCropMode.value);
};

const handleReset = () => {
  // Simple reset: reload the file logic or just reload page?
  // Ideally reset cropper.
  // We can just nullify and re-set, but easier to just clear
  currentFile.value = null;
  isCropMode.value = false;
};

const handleDownload = () => {
  const resultDataUrl = editorRef.value?.getResult();
  if (resultDataUrl && currentFile.value) {
    const link = document.createElement('a');
    link.download = `edited-${currentFile.value.name}`;
    link.href = resultDataUrl;
    document.body.appendChild(link);
    link.click();
    document.body.removeChild(link);
  }
};
</script>

<template>
  <div class="app-layout">
    <header>
      <div class="logo">
        <i class="ph ph-image"></i>
        <span>ImageTool</span>
      </div>
      <button v-if="currentFile" @click="handleReset" class="btn-text">
        <i class="ph ph-x"></i> Close
      </button>
    </header>

    <main>
      <Transition name="fade" mode="out-in">
        <ImageUploader 
          v-if="!currentFile" 
          @file-selected="handleFileSelected" 
        />
        <div v-else class="editor-workspace">
          <ImageEditor 
            ref="editorRef" 
            :file="currentFile" 
          />
          <Controls 
            :is-crop-mode="isCropMode"
            @rotate="handleRotate"
            @flip="handleFlip"
            @zoom="handleZoom"
            @toggle-crop="handleToggleCrop"
            @reset="handleReset"
            @download="handleDownload"
          />
        </div>
      </Transition>
    </main>
  </div>
</template>

<style scoped>
.app-layout {
  height: 100vh;
  display: flex;
  flex-direction: column;
  padding: 20px;
  box-sizing: border-box;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
  padding: 0 10px;
}

.logo {
  font-size: 24px;
  font-weight: 700;
  display: flex;
  align-items: center;
  gap: 10px;
  color: var(--text-primary);
}

.logo i {
  color: var(--accent-color);
  font-size: 32px;
}

main {
  flex: 1;
  display: flex;
  flex-direction: column;
  position: relative;
  overflow: hidden;
}

.editor-workspace {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
}

.btn-text {
  background: none;
  border: none;
  color: var(--text-secondary);
  cursor: pointer;
  font-size: 14px;
  display: flex;
  align-items: center;
  gap: 5px;
}
.btn-text:hover {
  color: var(--text-primary);
}

/* Transitions */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
