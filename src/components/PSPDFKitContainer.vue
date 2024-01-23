<template>
    <div>
        <v-btn rounded color="warning" dark class="mb-5" @click="downloadFile">
            Download file
        </v-btn>
        <div class="pdf-container"></div>
    </div>
</template>

<script>
import PSPDFKit from "pspdfkit";

export default {
    name: 'PSPDFKit',

    props: {
        pdfFile: {
            type: String,
            required: true,
        },
    },

    data() {
        return {
            pspdfKitLoad: null,
            pspdfKitLoadInstance: null,
            arrayBuffer: null,
        }
    },

    watch: {
        pdfFile(val) {
            if (val) {
                this.loadPSPDFKit();
            }
        },
    },

    methods: {
        async loadPSPDFKit() {
            PSPDFKit.unload(".pdf-container");
            return PSPDFKit.load({
                document: this.pdfFile,
                container: ".pdf-container",
            });
        },

        downloadFile() {
            const blob = new Blob([this.arrayBuffer], { type: "application/pdf" });
            const fileName = `isigma_document_${Date.now()}.pdf`;

            if (window.navigator.msSaveOrOpenBlob) {
                window.navigator.msSaveOrOpenBlob(blob, fileName);
            } else {
                const objectUrl = URL.createObjectURL(blob);
                const a = document.createElement("a");
                a.href = objectUrl;
                a.style = "display: none";
                a.download = fileName;
                document.body.appendChild(a);
                a.click();
                URL.revokeObjectURL(objectUrl);
                document.body.removeChild(a);
            }
        }
    },

    beforeUnmount() {
        PSPDFKit.unload(".pdf-container");
    },

    mounted() {
        this.loadPSPDFKit().then(async (instance) => {
            this.$emit("loaded", instance);

            instance.addEventListener("formFieldValues.update", async () => {
                instance.save();

                await instance.exportPDF().then((buffer) => {
                    this.arrayBuffer = buffer;

                });
            });
        });
    },
}
</script>

<style scoped>
.pdf-container {
    height: 100vh;
}
</style>