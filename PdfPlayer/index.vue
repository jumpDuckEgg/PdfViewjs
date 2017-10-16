<template>
    <section v-loading="!pdf_loaded">

        <pdf-viewer v-if="!!url"
        :src="url" 
        :page="currentPage"
        @GET-PDF-PAGES="getContentLength"
        @PDF-LOADED="pdfLoaded"
        @CURRENT-PAGE-LOADED="currentPageLoaded"></pdf-viewer>

        <el-row class="article-bar" type="flex" justify="space-around" v-loading="!page_loaded">
            <el-col :span="6" class="article-bar__first-column">
                <i class="fa fa-search-minus fa-lg" aria-hidden="true" @click="minus" v-if="showScaleBtn"></i>
                <i class="fa fa-search-plus fa-lg" aria-hidden="true" @click="plus" v-if="showScaleBtn"></i>
            </el-col>
            <el-col :span="10" class="article-bar__second-column">
                <el-button-group>
                    <el-button 
                    size="small" 
                    :disabled="currentPage <= 1"
                    @click="lastPage">
                        <i class="el-icon-arrow-left el-icon--left"></i>
                        上一页
                    </el-button>
                    <el-button
                    size="small" 
                    :disabled="currentPage >= docAllPage"
                    @click="nextPage">
                        下一页
                        <i class="el-icon-arrow-right el-icon--right"></i>
                    </el-button>
                </el-button-group>
            </el-col>
            <el-col :span="4" class="article-bar__third-column">
                <el-input size="small" class="article-bar__page-input"
                @keyup.native="inputNumber" 
                @keyup.native.enter="changeCurrentPage" 
                v-model="text"></el-input>/{{docAllPage}}
            </el-col>
        </el-row>

    </section>
</template>

<script>
import PdfViewer from './components/PdfViewer.vue'
export default {
    components: {
        'pdf-viewer': PdfViewer
    },
    props: {
        url: {
            type: String,
            required: true
        },
        page: {
            type: Number,
            default: 1
        },
        showScaleBtn: {
            type: Boolean,
            default: false
        }
    },
    data(){
        return {
            pdf_loaded: false,
            page_loaded: false,
            currentPage: 1,
            currentScale: 1,
            docAllPage: 0,
            buttonSize: 4,
            panel_left: 16,
            text: 1
        }
    },
    watch: {
        "currentPage"(val){
            // console.log('文档的当前页是：', val);
            this.page_loaded = false;
            this.$emit('CURRENT-PAGE-CHANGE', val);
        },
        "page"(val){
            if(!val || parseInt(val) === this.currentPage){
                return;
            }
            let num = parseInt(val);
            this.text = this.currentPage = num > 1 ? num : 1;
        }
    },
    methods: {
        minus() {
            this.$emit('PLAYER-MINUS');
        },
        plus() {
            this.$emit('PLAYER-PLUS');
        },
        lastPage() {
            if (this.currentPage > 1) {
                this.currentPage--;
                this.text = this.currentPage;
            }

        },
        nextPage() {
            if (this.currentPage < this.docAllPage) {
                this.currentPage++;
                this.text = this.currentPage;
            }
        },
        inputNumber(event) {
            if (this.text.length == 1){ 
                this.text = this.text.replace(/[^1-9]/g, '');
                return;
            }
            this.text = this.text.replace(/\D/g, '');
        },
        changeCurrentPage() {
            let pageNum = parseInt(this.text);
            if(pageNum < 1){
                this.currentPage = this.text = 1;
                return;
            }
            if(pageNum > this.docAllPage){
                this.currentPage = this.text = this.docAllPage;
                return;
            }
            this.currentPage = this.text = pageNum;
        },
        currentPageLoaded(){
            this.page_loaded = true;
            this.$emit('CURRENT-PAGE-LOADED', this.currentPage);
        },
        getContentLength(length){
            this.docAllPage = length;
        },
        pdfLoaded(){
            this.pdf_loaded = true;
        }
    }
}
</script>

<style lang="scss" scoped>
.article-bar{
    background-color: #9dd0ed;
    padding: 10px;
    &__first-column{
        line-height: 36px;
        color: white;
        cursor: pointer;
        letter-spacing: 5px;
    }
    &__second-column{
        height: 36px;
    }
    &__third-column{
        height: 36px;
    }
    &__page-input{
        width: 50px;
        font-size: 16px;
        text-align: right;
    }
}
</style>
