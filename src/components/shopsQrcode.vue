<template>
   <div class="continar">
       <div class="page-header position-relative">
           <div class="header-title">
               <h1>店铺二维码</h1>
           </div>
       </div>


       <div class="page-body">
           <div>
               <h5>搜索条件</h5>
               <select name="选择城市" class="shop_sel" v-model="selectCity"  >
                   <option value="0" selected="selected">选择城市</option>
                   <template  v-for="item in shopNameList" >
                       <option  >{{ item.name }}</option>
                   </template>


               </select>

               <input type="text" class="form-control conten_width time_width" placeholder="选择时间" v-date-range-picker="timeObj" :options="{format:'YYYY-MM-DD',language:'zh'}"/>
               <input type="text" class="form-control conten_width" v-model="searchShop" placeholder="输入店铺号名称/编号"/>
               <div class="searchBtn" @click="searchItem" >搜索</div>
           </div>

           <div class="shop_lis" >
               <div class="down_more_div">
                   <p class="downLoadMore"  @click.stop="fetchChunkShopFocusQRcode">批量下载</p>
               </div>

               <div class="qr_content">
                   <div class="qrhead">
                       <div  class="float_head ">
                               <input type="checkbox" name=" "   v-model="allChecked"  /><span class="slect_all">全选</span>
                       </div>
                       <div class="float_head pos">开通时间</div>
                       <div class="float_head pos">所在城市</div>
                       <div class="float_head pos">店铺名称</div>
                       <div class="float_head pos">操作</div>
                       <div class="clear_float pos"></div>
                   </div>

                   <div class="shop_list">
                       <template v-for="(index, item) in shoplistData">
                           <div class="qrhead" >
                               <div  class="float_head ">
                                   <input type="checkbox"   id="{{item.shopNo }}" value="{{item.shopNo }}"  v-model="pickedShops"   /><span class="slect_all">{{ index+1 }}</span>
                               </div>
                               <div class="float_head pos">{{ item.openTime}}</div>
                               <div class="float_head pos">{{item.city ? item.city : "--"}}</div>
                               <div class="float_head pos">{{item.shopName}}</div>
                               <div class="float_head pos down_qr" @click="downLoadQrcode(item.shopNo)">下载二维码</div>

                           </div>
                       </template>

                       <div class="row pagePlugin">
                           <pagination v-if="totalPage>1" :page-per-rows="rows" :total-page="totalPage" :cur-page.sync="curPage"  :total-row="totalRows"></pagination>
                       </div>

                   </div>

               </div>


           </div>


       </div>

   </div>

</template>
<style>
    .conten_width{width: 150px;display: inline-block}
    .shop_list{}
    .shop_sel{height: 35px;}
    .time_width{width: 222px;text-align: center;font-size:16px;}
    input[type="checkbox"]{opacity:1;position: inherit;margin-left: 8px;}
    .slect_all{position: relative;top:-3px;}
    .continar{width:auto}
    .down_more_div{width:auto;height: 40px;border: 1px solid #afafaf ;line-height: 40px;margin:5px auto;width: 99%;}
    .searchBtn{cursor:pointer; position:relative;top:2px; color:#fff; border-radius: 5px;display: inline-block;margin-left: 20px;width: 60px;height:30px;border: 1px solid #6f85bf;line-height: 30px;font-size:18px;text-align: center;background-color: #0b97c4}
    .downLoadMore{cursor:pointer; border-radius: 20px; margin-top:8px;font-size: 18px;width: 120px;background-color: #0b97c4;color: #fff;text-align: center;position: absolute;right: 5%}
    .shop_lis{margin-top: 10px;border: 1px solid #afafaf}
    .checkbox{display: inline-block;width: 20px;height: 20px;border: 0px solid darkseagreen;background-color: #fff;margin-left: 5px;}
    .checkbox_text{ display: inline-block;width: 40px; height: 20px; margin-top:-5px; position: relative;top:-15px;margin-left: 5px; }
    .float_head{ display: inline-block;width: 65px;}
     .pos{position: relative;font-size: 14px;width: 20%;text-align:center;top:-3px;}
    .down_qr{ color: #5fa6d3;cursor:pointer }
    .pagePlugin{ margin-left: 7px;margin-top: 15px;}

</style>
<script>

    import * as ajaxUtil from '../util/Ajax.js'
    import pagination from './common/Pagination.vue'
    var store = require('store')
    export default{
        data(){
            return{
                shopNameList:[  {name:"深圳市"},{name:"广州市"},
                                {name:"上海市"},{name:"苏州市"},{name:"重庆市"},
                                {name:"重庆市"},{name:"长沙市"},{name:"郑州市"},{name:"武汉市"},{name:"成都市"}],
                shoplistData:[],
                selectCity:"",
                searchShop:"",
                pickedShops:[],
                isSearch:false,
                selectAll:false,
                selectSigal:false,
                totalPage:0,
                totalRows:0,
                selectShops:"",
                timeObj:null,
                curPage:1,
                rows:20
            }
        },

        computed:{
            allChecked:{
                get:function(){
                    if(this.checkedCount<6){
                        return false
                    }else {
                        return true
                    }
                   // return this.checkedCount == 12;

                },
                set:function(value){
                    if(value){
                        var sliceShoplistData=this.shoplistData.slice(0,6)
                        this.pickedShops = sliceShoplistData.map(function(item){
                            return item.shopNo
                        })
                    }else {
                        this.pickedShops =[]
                    }

                }
            },
            checkedCount:{
                get: function(){
                    return this.pickedShops.length;

                }
            }

        },

        ready(){

        },

        components: {
            pagination
        },

        compiled(){
            this.updateData();
        },
        methods:{
            updateData (){
                let params = {};
                if(this.isSearch==false){
                    params.offset = this.curPage;
                    params.limit = this.rows;
                }else{
                    console.log("search");
                    if(this.timeObj != null){
                        params.start_time= this.timeObj.start_time.getTime() ;
                        params.end_time= this.timeObj.end_time.getTime()
                    }
                    params.city=encodeURI(this.selectCity);
                    params.shop_name= encodeURI(this.searchShop);

                }

                var url = Url.FETCH_SHOP_LIST;
                var self = this;
                ajaxUtil.doGet(url,params).then((xhr,response) =>{
                    var content = response;

                    if(content.returnCode =="success"){

                        if(content.shops.length>0){
                            self.isSearch=false;
                            this.shoplistData = content.shops;
                        }


                        this.totalRows = content.pageCount;
                        if(content.pageCount%this.rows == 0){
                            this.totalPage = Math.floor(content.pageCount/this.rows) ;
                        }else{
                            this.totalPage = Math.floor(content.pageCount/this.rows) +1;
                        }

                    }else {
                        return ajaxUtil.doError(response,this);
                    }



                })
            },


            fetchChunkShopFocusQRcode(){
                var _this=this;
               var params={};
                    if(this.pickedShops.length==0){
                        return
                    }
                    params.shop_nos= this.pickedShops.join("|");
                var url = Url.FETCH_BATCH_SHOP_FOCUS_QRCODE;

                ajaxUtil.doGet(url,params).then((xhr,response)=>{
                    if(response.returnCode=="success"){
                        console.log("response:",response);

                        store.remove('qrCodeList');
                        store.set('qrCodeList',response.codes);
                        console.log("qrCodeList:",response.codes)




                    var  url =Url. FETCH_BATCH_SHOP_PAY_QECODE;
                    ajaxUtil.doGet(url,params).then((xhr,response)=>{

                        if(response.returnCode == "success"){
                            console.log("支付",response.payCodes);

                            store.remove("payQrcodeList");
                            store.set("payQrcodeList",response.payCodes);

                            _this.$router.go({"path":"/download/shopsQRcode"});

                        }else{
                            return ajaxUtil.doError(response,this);
                        }

                    });

                    }else{
                        return ajaxUtil.doError(response,this);
                    }



                })

            },
            searchItem(){
                this.isSearch=true;
                this.updateData();
            },
            downLoadQrcode(shopNum){
                var _this = this;
                var url =Url.FETCH_SINGLE_SHOP_FOCUS_QRCODE+"/"+ shopNum + "/attention/qrcode";
                let params={};


                ajaxUtil.doGet(url,params).then((xhr,response)=>{
                    var content=response;
                    if (content.returnCode==="success") {
                     console.log("response:",content);

                        store.remove('qrCodeList');
                        store.set('qrCodeList',content.code);


                        var url =Url.FETVH_SINGLE_SHOP_PAY_QRCODE+shopNum+"/pay/qrcode"
                        ajaxUtil.doGet(url,{}).then((xhr,response)=>{
                            if(response.returnCode =="success"){

                                console.log("支付",response.payCode);

                                store.remove("payQrcodeList");
                                store.set("payQrcodeList",response.payCode);

                                _this.$router.go({"path":"/download/shopsQRcode"});
                            }else{
                                return ajaxUtil.doError(response,this);
                            }

                        });





                    }else {
                        return ajaxUtil.doError(response,this);
                    }


                })


            },

        },
        watch:{
            curPage () {
                this.updateData()
            },
            pickedShops(newVal,oldVal){
                if(newVal.length>6){
                    this.pickedShops=this.pickedShops.splice(0,7) ;
                }
            }
        }


    }



</script>
