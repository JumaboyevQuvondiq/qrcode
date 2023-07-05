<template>
  <div style="display: flex;">
    <pdfViewer></pdfViewer>
    <div>
      <button class="qr-save-btn" @click="qrSaveBtn">Save</button>
      <div class="position-info">
        <span>Qr Code X: {{ qrPosition.x }}</span><br>
        <span>Qr Code Y: {{ qrPosition.y }}</span><br>
        <span>Pdf Height: {{ pdfPositionY }}</span>
      </div>
    </div>
  </div>
</template>

<script>
import pdfViewer from './components/PdfViewer.vue'

export default {
  data() {
    return {
      qrPosition: {
        x: 0,
        y: 0
      },
      pdfPositionY: 0
    }
  },
  components: {
    pdfViewer
  },
  methods: {
    qrSaveBtn() {
      const qrCode = document.getElementById('qrCode');
      this.pdfPositionY = document.getElementById('mask').scrollTop
      const position = {
        x: 0,
        y: 0
      }
      const qrCodePosition = qrCode.style;
      position.x = qrCodePosition.marginLeft.replace('px', '');
      position.y = qrCodePosition.marginTop.replace('px', '');
      this.qrPosition.x = Math.floor(position.x)
      this.qrPosition.y = Math.floor(position.y)
      localStorage.setItem('qrPosition', JSON.stringify(this.qrPosition))
    }
  }
};
 
</script>

<style>


.position-info {
  margin-left: 5px;
  margin-top: 10px;
  font-size: 25px;
}

.qr-save-btn {
  margin-left: 5px;
  background-color: cornflowerblue;
  border: none;
  font-size: 30px;
  color: aliceblue;
  border-radius: 3px;
  cursor: pointer;
}
</style>