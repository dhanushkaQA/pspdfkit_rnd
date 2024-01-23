<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12">
        <div class="text-center">
          <v-btn rounded color="warning" dark @click="fillForm()" class="mr-4">
            Fill Form
          </v-btn>
          <v-btn rounded color="success" dark @click="savePdf()">
            Save PDF
          </v-btn>
        </div>

        <pdf :src="pdfSrc" ref="pdfViewer"></pdf>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
// import { PDFDocument, rgb, degrees } from 'pdf-lib';
import { PDFDocument } from 'pdf-lib';
import pdf from 'vue-pdf';
import samplePdf from '../assets/pdf/sample_pdf.pdf';

export default {
  components: {
    pdf,
  },

  data: () => ({
    pdfSrc: samplePdf,
    pdfDoc: null,
    pdfBytes: null,
  }),

  methods: {
    async onPdfLoaded() {
      fetch(this.pdfSrc).then((response) => {
        return response.arrayBuffer()
      }).then(async (data) => {
        this.pdfDoc = await PDFDocument.load(data);
      }, function (err) {
        console.log(err);
      });
    },
    async fillForm() {
      if (!this.pdfDoc) return;

      const form = this.pdfDoc.getForm();

      const nameField = form.getTextField('Name');
      const addressField = form.getTextField('Address');

      nameField.setText('John Doe');
      addressField.setText('This is sample address');

      const pdfBytes = await this.pdfDoc.save();
      this.$refs.pdfViewer.pdf.loadDocument(pdfBytes);
    },
    async savePdf() {
      if (this.pdfDoc) {
        const blob = new Blob([await this.pdfDoc.save()], { type: 'application/pdf' });
        const link = document.createElement('a');
        link.href = window.URL.createObjectURL(blob);
        link.download = 'filled_form.pdf';
        link.click();
      }
    }
  },

  mounted() {
    setTimeout(() => {
      this.onPdfLoaded()
    }, 1000);
  }
}
</script>
