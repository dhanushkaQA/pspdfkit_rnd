<template>
	<v-app>
		<v-app-bar app color="#043351" dark>
			<div class="d-flex align-center">
				<v-img alt="Vuetify Logo" class="shrink mr-2" contain src="./assets/logo.png" transition="scale-transition"
					width="160" />
			</div>
			<v-spacer></v-spacer>
			<v-toolbar-title>Fillable Forms</v-toolbar-title>
			<v-spacer></v-spacer>
		</v-app-bar>

		<v-main>
			<v-container class="pt-8">
				<v-file-input label="Select PDF" outlined dense @change="openDocument" @click:clear="openDocument"
					chips></v-file-input>

				<hr />
				<PSPDFKitContainer :pdfFile="pdfFile" @loaded="handleLoaded" />
			</v-container>
		</v-main>
	</v-app>
</template>

<script>
import PSPDFKitContainer from "@/components/PSPDFKitContainer";
import samplePdf from './assets/pdf/sample_pdf.pdf';

export default {
	name: 'App',

	components: {
		PSPDFKitContainer
	},

	data() {
		return {
			pdfFile: this.pdfFile || samplePdf,
		};
	},

	methods: {
		handleLoaded(instance) {
			console.log("PSPDFKit has loaded: ", instance);
		},

		openDocument(selectedPdfFile) {
			if (this.pdfFile && this.pdfFile.startsWith('blob:')) {
				window.URL.revokeObjectURL(this.pdfFile);
			}

			if (selectedPdfFile && selectedPdfFile.type == 'application/pdf') {
				this.pdfFile = window.URL.createObjectURL(selectedPdfFile);
			} else {
				this.pdfFile = samplePdf;
			}
		},
	},
};
</script>

<style scoped>
input[type="file"] {
	display: none;
}

.custom-file-upload {
	border: 1px solid #ccc;
	border-radius: 4px;
	display: inline-block;
	padding: 6px 12px;
	cursor: pointer;
	background: #4A8FED;
	padding: 10px;
	color: #fff;
	font: inherit;
	font-size: 16px;
	font-weight: bold;
}

hr {
	margin: 25px 0;
}

.v-toolbar__title {
	letter-spacing: 3px;
	text-transform: uppercase;
}
</style>
