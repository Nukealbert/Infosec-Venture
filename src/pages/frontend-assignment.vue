<template>
  <div id="file-drop-area" @drop.prevent="handleDrop" @dragover.prevent="handleDragOver">
    <div v-if="!uploading">
      <p>Drag & Drop files here</p>
      <input type="file" ref="fileInput" @change="handleFileSelect" style="display: none" />
      <button @click="openFileInput" class="browse-btn">or Click to select files</button>
    </div>
    <div v-else>
      <p>Uploading...</p>
      <div>{{ progress }}%</div>
      <progress :value="progress" max="100"></progress>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      uploading: false,
      progress: 0,
    };
  },
  methods: {
    openFileInput() {
      this.$refs.fileInput.click();
    },
    handleFileSelect(event) {
      const files = event.target.files;
      this.uploadFiles(files);
    },
    handleDragOver(event) {
      event.dataTransfer.dropEffect = 'copy';
    },
    handleDrop(event) {
      event.preventDefault();
      const files = event.dataTransfer.files;
      this.uploadFiles(files);
    },
    uploadFiles(files) {
      this.uploading = true;

      const totalSize = Array.from(files).reduce((acc, file) => acc + file.size, 0);
      let uploadedSize = 0;

      Array.from(files).forEach((file) => {
        const formData = new FormData();
        formData.append('file', file);

        const xhr = new XMLHttpRequest();

        xhr.upload.addEventListener('progress', (e) => {
          if (e.lengthComputable) {
            uploadedSize += e.loaded;
            this.progress = Math.round((uploadedSize / totalSize) * 100);
          }
        });

        xhr.onreadystatechange = () => {
          if (xhr.readyState === 4) {
            // Handle the response from the server if needed
            // For simplicity, we're not handling the response in this example
            // You might want to add error handling and success messages here
          }
        };

        xhr.open('POST', '/your-upload-endpoint', true);
        xhr.send(formData);
      });
    },
  },
};
</script>

<style scoped>
#file-drop-area {
  border: 2px dashed #3498db;
  border-radius: 8px;
  padding: 20px;
  text-align: center;
  font-family: 'Arial', sans-serif;
  color: #555;
}

.upload-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.drop-text {
  font-size: 1.2em;
  margin: 0;
}

.browse-btn {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1em;
  transition: background-color 0.3s;
}

.browse-btn:hover {
  background-color: #2980b9;
}

.uploading-container {
  margin-top: 20px;
}

/* Additional styles for progress bar */
progress {
  width: 100%;
  margin-top: 10px;
}
</style>
