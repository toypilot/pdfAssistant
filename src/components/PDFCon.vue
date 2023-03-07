<template>
    <div>
        <div ref="docCon"></div>
    </div>
</template>
<script>
import * as pdfjsLib from 'pdfjs-dist';
import PDFViewer from 'pdfjs-dist/web/pdf_viewer'
import "pdfjs-dist/web/pdf_viewer.css"

export default {
    methods: {
        makePage(page) {
            const canvas = document.createElement('canvas');
            const context = canvas.getContext('2d');
            const viewport = page.getViewport({ scale: 1.0 });
            canvas.height = viewport.height;
            canvas.width = viewport.width;
            this.$refs.docCon.appendChild(canvas)
            return {context , viewport}           
        },
        async open() {
            let pdfUrl = 'https://pdftron.s3.amazonaws.com/downloads/pl/demo-annotated.pdf'
            let pdfObj = await pdfjsLib.getDocument(pdfUrl).promise

            let pageNum = 1
            while(pageNum <= pdfObj.numPages){
                let page = await pdfObj.getPage(pageNum)                
                let {context, viewport } = this.makePage(page)
                page.render({
                    canvasContext: context,
                    viewport: viewport
                    })
                    pageNum++
            }
        },
    },
    mounted() {
        pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://cdnjs.cloudflare.com/ajax/libs/pdf.js/3.4.120/pdf.worker.min.js'
        this.open()
    }
}
</script>
