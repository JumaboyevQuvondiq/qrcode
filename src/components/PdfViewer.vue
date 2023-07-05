<template>
    <div id="mask" @dragover.prevent @click="dragEnd">
        <pdf v-for="page in totalpages" class="pdfViewer" src="sample_pdf.pdf" :page="page" />
        <div id="qrcodeDiv">
            <div>
                <img id="qrCode" src="https://api.qrserver.com/v1/create-qr-code/?data=Simple Code&amp;size=80x80"
                style="border:1px solid #62CB31; box-shadow: 0px 0px 10px #62CB31;" draggable="true" @dragend="dragEnd">
            </div>
        </div>
    </div>
</template>
  
<script>
import pdf from 'pdfvuer'

export default {
    data() {
        return {
            item: 'qrCode',
            mockPostion: { x: 0, y: 0 },
            totalpages: 0
        };
    },
    components: {
        pdf,
    },
    methods: {
        getpdfInfo() {
            var self = this;
            self.pdfdata = pdf.createLoadingTask('example.pdf');
            self.pdfdata.then(pdf => {
                self.totalpages = pdf.numPages;
            })
        },
        getQrPosition() {
            const position = localStorage.getItem('qrPosition');
            if (position) {
                return JSON.parse(position)
            }
            else {
                return { x: 0, y: 0 }
            }
        }, calculatePosition(position) {
            const paddingX = 15;
            const paddingY = 15;
            const maskdiv = document.getElementById('qrcodeDiv');
            const calcPosition = { x: paddingX, y: paddingY };
            const box = document.getElementById('qrCode');
            if (position.x < paddingX || (position.x + box.offsetWidth > maskdiv.offsetWidth - paddingX)) {
                if (position.x + box.offsetWidth > maskdiv.offsetWidth - paddingX) {
                    calcPosition.x = position.x - (position.x + box.offsetWidth - maskdiv.offsetWidth) - paddingX;
                }
            } else {
                calcPosition.x = position.x
            }
            if (position.y < paddingY || (position.y + box.offsetHeight > maskdiv.offsetHeight - paddingY)) {
                if (position.y + box.offsetHeight > maskdiv.offsetHeight - paddingY) {
                    calcPosition.y = position.y - (position.y + box.offsetHeight - maskdiv.offsetHeight) - paddingY;
                }
            } else {
                calcPosition.y = position.y
            }
            return calcPosition
        },
        dragEnd(event) {
            const qrcodeDiv = document.getElementById('qrcodeDiv').getBoundingClientRect();
            const x = event.pageX - (event.pageX - event.clientX + qrcodeDiv.x) - this.mockPostion.x;
            const y = event.pageY - (event.pageY - event.clientY + qrcodeDiv.y) - this.mockPostion.y;
            const position = { x: x, y: y };
            const calcPosition = this.calculatePosition(position);
            const qrCode = document.getElementById('qrCode');
            qrCode.style.marginLeft = calcPosition.x + 'px';
            qrCode.style.marginTop = calcPosition.y + 'px';

        }
    },
    mounted() {
        this.getpdfInfo()
        const qrCode = document.getElementById('qrCode');
        const position = this.getQrPosition();
        qrCode.style.position = 'sticky';
        qrCode.style.marginLeft = position.x + 'px';
        qrCode.style.marginTop = position.y + 'px';
        this.mockPostion.x = 40,
        this.mockPostion.y = 40
       
        const qrcodeDiv = document.getElementById('qrcodeDiv')
        document.getElementById('mask').addEventListener("scroll", evt =>
            qrcodeDiv.style.top = evt.target.scrollTop + 'px'
        )
    }
};
</script>


<style>
#mask {
    width: 1153px;
    height: 900px;
    position: relative;
    justify-content: center;
    cursor: pointer;
    border: 1px solid black;
    overflow-y: scroll;
    background-color: transparent;
}

.pdfViewer {
    width: 98%;
    margin: 5px;
}

#qrcode {
    border: 10px solid red;
    box-shadow: 5px;
    position: sticky;
    height: 80px;
    width: 80px;
}

#qrcodeDiv {
    position: absolute;
    top: 3px;
    left: 4px;
    width: 99%;
    height: 98%;
}


</style>