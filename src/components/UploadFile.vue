<template>
  <div class="file-upload">
    <ElButton type="primary"><input type="file" @change="handleFileChange" ref="fileInput" /></ElButton>
    <ElButton @click="detectPermissionFields">CHECK</ElButton>
  </div>
  <div class="split-screen">
    <div class="left-column">
      <div v-if="file1">
        <h2>File Raw</h2>
        <iframe :src="fileUrl1" width="100%" height="500px" frameborder="0"></iframe>
      </div>
    </div>
    <div class="right-column">
      <div v-if="file2">
        <h2>File Result</h2>
        <iframe :src="fileUrl2" width="100%" height="500px" frameborder="0"></iframe>
      </div>
    </div>
  </div>
</template>

<script>
import { ElButton } from "element-plus";
import { PDFDocument, rgb } from 'pdf-lib';
const FORM_COLOR = {
  // #E08F00
  CAUTION_BORDER_COLOR: rgb(224 / 255, 143 / 255, 0 / 255),
  // #FEF6E7
  CAUTION_FILL_COLOR: rgb(254 / 255, 246 / 255, 231 / 255),
  // #0bb4ba
  OLIVIA_BORDER_COLOR: rgb(11 / 255, 180 / 255, 186 / 255),
  // #25C9D0
  OLIVIA_FILL_COLOR: rgb(37 / 255, 201 / 255, 208 / 255),
  // #cc0000
  REQUIRED_COLOR: rgb(204 / 255, 0 / 255, 0 / 255),
  // #ffd966
  READONLY_COLOR: rgb(255 / 255, 217 / 255, 102 / 255),
};

export default {
  name: "UploadFile",
  components: {
    ElButton,
  },
  data() {
    return {
      file1: null,
      fileUrl1: "",
      file2: null,
      fileUrl2: "",
    };
  },
  methods: {
    async detectPermissionFields() {
      this.file2 = await this.drawRectangles(this.fileBuffer1);
      const blob = new Blob([this.file2], { type: 'application/pdf' });
      this.fileUrl2 = URL.createObjectURL(blob);
    },
    async handleFileChange(event) {
      this.file1 = event.target.files[0];
      this.fileUrl1 = URL.createObjectURL(this.file1);
      // Read the file as an ArrayBuffer
      const reader = new FileReader();
      reader.onload = (event) => {
        this.fileBuffer1 = event.target.result;
      };
      reader.readAsArrayBuffer(this.file1);
      // await this.detectPermissionFields();
    },
    getColorRGB(hex, colorDefault) {
      if (hex) {
        const hexValue = hex.replace("#", "");
        const r = parseInt(hexValue.substring(0, 2), 16);
        const g = parseInt(hexValue.substring(2, 4), 16);
        const b = parseInt(hexValue.substring(4, 6), 16);
        return rgb(r / 255, g / 255, b / 255);
      }

      return colorDefault;
    },
    getPage(pdfDoc, widget) {
      const pages = pdfDoc.getPages();
      const firstPage = pages[0];

      const pageRef = widget.P();
      const widgetPage = pages.find((x) => x.ref === pageRef);

      if (widgetPage) {
        return widgetPage;
      }

      const widgetRef = pdfDoc.context.getObjectRef(widget.dict);

      if (widgetRef === undefined) {
        return firstPage;
      }

      const objectPage = this.pdfDoc.findPageForAnnotationRef(widgetRef);

      if (objectPage) {
        return objectPage;
      }

      return firstPage;
    },
    getFieldStyle(
      field,
      operations,
    ) {
      const {
        highlightColor,
        highlightBorderColor,
      } = operations;

      if (field.isReadOnly()) {
        console.log('isReadOnly', field.getName());
        return {
          borderColor: this.getColorRGB(
            highlightBorderColor,
            FORM_COLOR.READONLY_COLOR,
          ),
          color: this.getColorRGB(highlightColor, FORM_COLOR.READONLY_COLOR),
          opacity: 0.1,
        };
      }
      if (field.isRequired()) {
        console.log('isRequired', field.getName());
        return {
          borderColor: this.getColorRGB(
            highlightBorderColor,
            FORM_COLOR.REQUIRED_COLOR,
          ),
          color: this.getColorRGB(highlightColor, FORM_COLOR.REQUIRED_COLOR),
          opacity: 0.1,
        };
      }


      return {
        borderColor: this.getColorRGB(
          highlightBorderColor,
          FORM_COLOR.OLIVIA_BORDER_COLOR,
        ),
        color: this.getColorRGB(highlightColor, FORM_COLOR.OLIVIA_FILL_COLOR),
        opacity: 0.1,
      };
    },
    async drawRectangles(pdfBytes) {
      try {
        // Load the PDF document
        const pdfDoc = await PDFDocument.load(pdfBytes);

        const form = pdfDoc.getForm()
        const fieldNames = form.getFields().map((field) => field.getName());
        fieldNames.map((fieldName) => {

          const field = form.getField(fieldName);

          field.acroField.getWidgets().forEach((widget) => {
            const page = this.getPage(pdfDoc, widget);

            const { x, y, width, height } = widget.getRectangle();

            const fieldStyle = this.getFieldStyle(field, {});
            page.drawRectangle({
              x: x,
              y: y,
              width: width,
              height: height,
              borderColor: fieldStyle.borderColor,
              color: fieldStyle.color,
              borderWidth: 1,
              opacity: fieldStyle.opacity,
            });
          });
        });
        // Save the modified PDF document
        const resultBytes = await pdfDoc.save();
        return resultBytes.buffer
      } catch (error) {
        console.error('Error:', error);
      }
    }

  },


};
</script>

<style scoped>
.split-screen {
  display: flex;
  flex-direction: row;
}

.left-column {
  flex: 1;
  background-color: #f0f0f0;
}

.right-column {
  flex: 1;
  background-color: #e0e0e0;
}

.file-upload {
  margin: 20px;
}

@media (max-width: 768px) {
  .split-screen {
    flex-direction: column;
  }

  .left-column,
  .right-column {
    flex: none;
  }
}
</style>
