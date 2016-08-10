<template>
    <div class="continar">
        <div class="page-header position-relative">
            <div class="header-title">
                <h1>下载店铺二维码</h1>
            </div>
        </div>


        <div class="page-body page-body-download">
            <template v-if="qrCodeList.length>0">
                <template  v-for="item in qrCodeList">
                    <div class="cavans_container">

                        <div class="cavas_text"  v-bind:id= " item.shopNo "  >

                        </div>
                        <div class="text_info">
                            <p class="shopName" v-text="item.shopName"></p>
                            <p class="shopNo" v-text=" item.shopNo"> </p>
                        </div>
                    </div>
                </template>
            </template>

            <template v-else>
                <div class="cavans_container">


                    <div class="cavas_text"  v-bind:id= "qrCodeList.shopNo"  >

                    </div>
                    <div class="text_info">
                        <p class="shopName" v-text="qrCodeList.shopName"></p>
                        <p class="shopNo" v-text=" qrCodeList.shopNo"> </p>
                    </div>
                </div>


            </template>

        </div>

    </div>
</template>
<style>
    .page-body-download{text-align:center;width:630pt;margin: 0px auto;}
   .cavans_container{display: inline-block;margin-left: 15px;}
   .cavas_text{padding: 5px; border: 1px solid gray;background-color: #ffffff; border-bottom: 0px solid gray}
    .shopName{text-align: center;font-weight:600}
    .shopNo{text-align: center;position:relative;top:-14px; }
    .text_info{line-height: 40px; border: 1px solid gray;height:40px;background-color: #ffffff;border-top: 0px solid gray; }
    canvas{display: block}
    canvas:nth-child(2){margin-top: 10px;border-top: 1px solid gray;padding-top: 7px; }


</style>
<script>
    import * as ajaxUtil from '../util/Ajax.js'
    var store = require('store');
    export default{
        data(){
            return{
                qrCodeList:[],
                qrPayCodeList:[],
                qrPayCodeList:[]
            }
        },
        beforeCompile(){
            this.getqQrCodeList();
            this.getPayQrShopList();

        },
        compiled(){

        },
        methods:{
            getqQrCodeList:function(){
                var qrCodeLis =store.get("qrCodeList")
                this.qrCodeList=qrCodeLis;
                console.log("qrCodeLis:",qrCodeLis)
            },
            getPayQrShopList:function(){
                var qrPayCodeList = store.get("payQrcodeList");
                this.qrPayCodeList=qrPayCodeList;
            },
            generateQRImg:function(item){
                var _this = this;

                var qrnode= new AraleQRCode({
                    render: 'canvas',
                    correctLevel: 0,
                    text: item.codeUrl ,
                    size: 190,
                    background: '#eeeeee',
                    foreground: '#667766',
                    pdground: '#00aaee',
                    image : DOWNLOAD_URL+"/"+item.logoName ,
                    imageSize :25
                });
                if(item.logoName){
                   qrnode.id=item.shopNo;
                    var ctx     = qrnode.getContext('2d');

                    ctx.lineWidth = 10;
                    ctx.strokeStyle = "#fff";

                    ctx.strokeRect(103, 103, 31, 31);
                }



                /*ctx.moveTo(68,70);//第一个起点
                ctx.lineTo(70,120);
                ctx.lineTo(120,120);
                ctx.lineTo(120,70);
                ctx.lineTo(68,70);*/

               // ctx.stroke()


                setTimeout(function(){
                    if(qrnode){
                        document.getElementById(item.shopNo).appendChild(qrnode);
                    }


                },500)

            }


        },
        ready(){


           if(Object.prototype.toString.call(this.qrPayCodeList)=='[object Array]'){
                for(var i= 0;i<this.qrPayCodeList.length;i++){
                    this.generateQRImg(this.qrPayCodeList[i]);
                }

            }else  {
                this.generateQRImg(this.qrPayCodeList);
            }

            if(Object.prototype.toString.call(this.qrCodeList)=='[object Array]'){
                for(var i= 0;i<this.qrCodeList.length;i++){
                    this.generateQRImg(this.qrCodeList[i]);
                }

            }else  {
               this.generateQRImg(this.qrCodeList);
            }






        }

    }
</script>
