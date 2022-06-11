<template>
  <q-page class="row items-center justify-evenly" @drop="dropThat" @dragover="dragoverThat" >
    <div  v-if="!dataBase64" align="center" style="font-size: 3em">
      <q-icon name="post_add" />
      <div>drop file</div>
    </div>
    <div v-else>
      <q-btn class="glossy" round color="primary" icon="restore_page" style="position: fixed;right: 0.5em;bottom: 3em" @click="clearFile" />
      <iframe :src="dataBase64"  :class="frameStyle" />
    </div>

  </q-page>
</template>

<script lang="ts" setup>
import {ref} from "vue";
import {PDFDocument} from "pdf-lib";
// @ts-ignore
let myWindowAPI = window.myWindowAPI;
let fs = myWindowAPI.getFS();
let dataBase64 = ref()
let frameStyle = ref("mainFrame")
const os = myWindowAPI.getOS();
if (os.platform() === 'darwin') {
  frameStyle.value = "fullFrame"
}
async function createPdf(input: string) {

  let file = fs.readFileSync(input,"base64");
  const pdfDoc = await PDFDocument.load(file)
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

function clearFile () {
  dataBase64.value = undefined
}

</script>

<style lang="sass">
.mainFrame
  width: 100vw
  height: calc(100vh - 2.8em)
.fullFrame
  width: 100vw
  height: 100vh
</style>

