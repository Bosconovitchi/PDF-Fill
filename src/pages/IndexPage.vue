<template>
  <q-page class="row items-center justify-evenly" @drop="dropThat" @dragover="dragoverThat">
    <embed :ref="mainFrame" v-if="!!dataBase64" :src="dataBase64" srcdoc=""  style="height: 100vh;width: 100vw" />
  </q-page>
</template>

<script lang="ts" setup>
import { ref} from "vue";
import {PDFDocument} from "pdf-lib";

const fs = require("fs") as typeof import("fs");
let dataBase64 = ref()
let mainFrame = ref()
async function createPdf(input: string) {
  const pdfDoc = await PDFDocument.load(fs.readFileSync(input,"base64"))
  const form = pdfDoc.getForm()
  const fields = form.getFields()
  fields.forEach((field) => {
    form.getTextField(field.getName()).setText("TEXT")
  })
  const pdfBytes = await pdfDoc.save()
  dataBase64.value = "data:application/pdf;base64,"+cover(pdfBytes)
}
function cover (array: Uint8Array) {
  let binary = "";
  for (let len = array.byteLength,i = 0;i < len; i++) {
    binary += String.fromCharCode(array[i])
  }
  return window.btoa(binary).replace(/=/g,"")
}

 function dropThat (e:any){
  e.preventDefault();
  e.stopPropagation();
  let files=e.dataTransfer.files;
  if(files.length>0)
  {
    createPdf(files[0].path)

  }

}

function dragoverThat (e:any) {
  e.preventDefault();
  e.stopPropagation();
}

window.ondragover = () => {
  console.log(1)
}



</script>
