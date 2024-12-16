<script setup>
import {ref} from "vue";
const textToEdit = ref("Write down the file to be encoded or drag and drop a encrypted file.");
const textToEditElement = ref(null)
const file = ref(null)
const isDragging = ref(false)
const reader = new FileReader();
const dragover = (e) => {
  e.preventDefault();
  isDragging.value = true;
}

const dragleave = () => {
  isDragging.value = false;
}

const drop = (e) => {
  e.preventDefault();
  editFile(e.dataTransfer.files);
  file.value = e.dataTransfer.files[0];
  isDragging.value = false;
}

reader.onload = (res) => {
  textToEdit.value = atob(atob(res.target.result));
};
reader.onerror = (err) => console.log(err);

function validate() {
  textToEdit.value = textToEditElement.value.innerText
}

function downloadNewVersion() {
  let encryptedText = btoa(btoa(textToEdit.value));
  debugger
  var fileToDownload = new File([encryptedText], file.value?.name ?? "newDocument.txt")
  var downloadElement = window.document.createElement("a");
  let href = URL.createObjectURL(fileToDownload);
  downloadElement.href = href;
  downloadElement.download = fileToDownload.name;
  document.body.appendChild(downloadElement);
  downloadElement.click();
  document.body.removeChild(downloadElement);
  window.URL.revokeObjectURL(href);
}

function editFile(files) {
  reader.readAsText(files[0]);
}

</script>

<template>
  <div
      class="container"
      @dragover="dragover"
      @dragleave="dragleave"
      @drop="drop"
  >
    <h1 class="h1" v-if="file == null">File editor</h1>
    <h1 class="h1" v-else>{{ file.name }}</h1>
    <div class="text-editor b-1 dragging d-flex" v-if="isDragging">
      <p>
        Release to drop files here.
      </p></div>
    <div
        v-else
        class="text-editor border p-2"
        ref="textToEditElement"
        lang="en"
        contenteditable="true"
        @keyup="validate"
    >
      {{ textToEdit }}
    </div>
      <div class="row sticky-bottom">
        <div class="col">
          <div class="btn-group mb-3 w-100" role="group">
            <button
                class="btn btn-primary btn-lg w-75 mt-3"
                @click="downloadNewVersion"
                type="button"
            >
              Download
            </button>
            <button
                data-bs-toggle="tooltip"
                data-bs-placement="top"
                data-bs-title="Tooltip on top"
                class="btn btn-outline-primary btn-lg mt-3"
                @click="downloadNewVersion"
                type="button"
            >
              <i class="bi bi-clipboard-fill"></i>
            </button>
          </div>
        </div>
      </div>
  </div>
</template>

<style scoped>
  .dragging {
    text-align: center;
    align-items: center;
    font-size: 30px!important;
    background-color: rgba(0,0,0,0.05);
    border-radius: 15px;
  }
  .dragging p {
    margin: auto!important;
    font-size: 50px!important;
    font-weight: 700!important;
    color: rgba(0,0,0,0.15)!important;
  }
  .text-editor {
    min-height: 100%;
    display: block;
    white-space: preserve;
    font-size: 20px;
    overflow-x: auto;
    text-wrap: nowrap;
  }

</style>