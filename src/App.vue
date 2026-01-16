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
  <div class="min-h-screen bg-neutral-950 flex items-center justify-center p-4">
    <div class="w-full max-w-xl bg-[#1E1E24] border border-amber-500 rounded-xl shadow-[0_0_30px_rgba(245,158,11,0.3)] overflow-hidden">
      <header class="flex justify-between items-center p-6 border-b border-amber-500/20">
        <div class="flex items-center gap-2 text-amber-500">
          <i class="ph ph-image text-2xl"></i>
          <span class="text-xl font-bold">ImageTool</span>
        </div>
        <button v-if="currentFile" @click="handleReset" class="flex items-center gap-1 text-gray-400 hover:text-amber-500 transition-colors text-sm bg-transparent border-none cursor-pointer">
          <i class="ph ph-x"></i> Close
        </button>
      </header>

      <main class="p-6 flex-1 flex flex-col relative">
        <Transition name="fade" mode="out-in">
          <ImageUploader 
            v-if="!currentFile" 
            @file-selected="handleFileSelected" 
          />
          <div v-else class="w-full flex flex-col items-center gap-6">
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
  </div>
</template>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
