<template>
    <div class="continar">
        <div class="page-header position-relative">
            <div class="header-title">
                <h1><a v-link="{ path: '/beautiful'}">美丽秘籍</a> > 设置文章</h1>
            </div>
        </div>


        <div class="page-body">
            <div class="shop_lis" >
                <div class="down_more_div">
                    <div class="searchBtn" style="width:90px" @click="showBeaMaterial=true">添加文章</div>

                    <div class="searchBtn" style="width:90px" v-if="isSort" @click="sortTabFun()" >调整排序</div>
                    <div style="display: inline;" v-else>
                        <div class="searchBtn" style="width:90px" @click="confirmAndCancel('firm')">保存</div>
                        <div class="searchBtn" style="width:90px" @click="confirmAndCancel('cancel')" >取消</div>
                    </div>
                </div>
            </div>
            <table class="table table-hover" id="table">
                <thead>
                <tr>
                    <th class="bor" >名称</th>
                    <th class="bor">排序</th>
                    <th class="bor">设为头条</th>
                    <th class="bor">操作</th>
                </tr>
                </thead>
                <tbody>
                    <tr v-if="ArtList.length == 0" ><td colspan = "4" >暂无数据!</td></tr>
                    <tr  v-else  v-for="(index, item) in ArtList">
                        <!--名称-->
                        <td class="bor" ><a :href="item.url" target="_blank" v-text="item.title"></a></td>
                        <!--排序-->
                        <td class="bor" v-if="isSort" v-text="item.sortNum"></td>
                        <td class="bor" v-else><input type="text"  onkeyup="this.value=this.value.replace(/\D/g,'')"  v-model="item.sortNum"/> </td>

                        <td class="bor">
                            <div class="searchBtn" style="width:90px" v-if="item.isCareSort==0"  @click="setOrCanerHot(item.id)">设为头条</div>
                            <div class="searchBtn" style="width:90px" v-else @click="setOrCanerHot(item.id)">取消头条</div>
                        </td>
                        <td class="bor">
                            <div class="searchBtn" style="width:90px" @click="deleteCategory(item.id)">删除</div>

                        </td>

                    </tr>

                </tbody>

            </table>
            <pagination v-if="totalPage>1" :page-per-rows="rows" :total-page="totalPage" :cur-page.sync="curPage" :total-row="totalRows"></pagination>
        </div>

    </div>


    <modal :show.sync="showBeaMaterial" title="选择文章" width="1000px">
        <div slot="modal-body">
            <beautiful-material  :media-param.sync="mediaParam" ></beautiful-material>
        </div>

        <div slot="modal-footer" class="modal-footer">
            <a class="btn btn-sky" @click="importMaterial()">导入</a>
        </div>
    </modal>

</template>
<style>
     .text-overflow {
         display:block;/*内联对象需加*/
         /*width:31em*/;/*指定宽度*/
         word-break:keep-all;/* 不换行 */
         white-space:nowrap;/* 强制在同一行内显示所有文本，直到文本结束或者遭遇 br 对象。不换行 */
         overflow:hidden;/* 内容超出宽度时隐藏超出部分的内容 */
         text-overflow:ellipsis;/* IE 专有属性，当对象内文本溢出时显示省略标记(...) ；需与overflow:hidden;一起使用。*/
     }
     .conten_width{width: 150px;display: inline-block}
     .shop_list{}
     .shop_sel{height: 35px;}
     .time_width{width: 222px;text-align: center;font-size:16px;}
     input[type="checkbox"]{opacity:1;position: inherit;margin-left: 8px;}
     .slect_all{position: relative;top:-3px;}
     .continar{width:auto}
     .down_more_div{width:auto;height: 40px;border: 1px solid #afafaf ;line-height: 40px;margin:5px auto;width: 99%;}
     .searchBtn{cursor:pointer; position:relative;top:2px; color:#fff; border-radius: 5px;display: inline-block;margin-left: 20px;width: 60px;height:30px;border: 1px solid #6f85bf;line-height: 30px;font-size:18px;text-align: center;background-color: #0b97c4}
     .beautiful{cursor:pointer; border-radius: 20px; margin-top:8px;font-size: 18px;width: 120px;background-color: #0b97c4;color: #fff;text-align: center;}
     .shop_lis{margin-top: 10px;border: 1px solid #afafaf}
     .checkbox{display: inline-block;width: 20px;height: 20px;border: 0px solid darkseagreen;background-color: #fff;margin-left: 5px;}
     .checkbox_text{ display: inline-block;width: 40px; height: 20px; margin-top:-5px; position: relative;top:-15px;margin-left: 5px; }
     .float_head{ display: inline-block;width: 65px;}
     .pos{position: relative;font-size: 14px;width: 18%;text-align:center;top:-3px;}
     .down_qr{ color: #5fa6d3;cursor:pointer }
     .pagePlugin{ margin-left: 7px;margin-top: 15px;}
     .bor{width:25%;border:1px solid #eee;text-align:center;}
     .layui-layer{background-color:#ffffff;}
     .layui-layer-content{color:black;background:cornsilk;padding:8px;}
 </style>

<script>
    import * as ajaxUtil from '../util/Ajax.js'
    import beautifulMaterial from './beautifulMaterial.vue'
    import pagination from './common/Pagination.vue'
    import modal from './common/Modal.vue'
    export default{
        data(){
            return {
                msg: '美丽秘籍',
                ArtList :[], //文章
                sortList :[],//排序的入参
                showBeaMaterial:false,//显示添加文章弹出遮罩组件
                mediaParam : [] ,//选中素材id,
                type_id :"", //属于什么类型
                curPage:1, //当前页
                rows:15,
                totalPage:0,
                totalRows:0,
                isSort :true//排序和保存取消按钮切换

            }
        },//计算属性
        computed:{

        },
        //组件
        components: {
            modal,
           beautifulMaterial,
           pagination
        },//编译
        compiled(){
           this.type_id = this.$route.params.typeId ;//获取分类id url传入
            this.getArtList();//获取数据
        },
        ready(){

        },
        methods : {
            sortTabFun(){
              //排序点击事件
             var  _this = this ;
              _this.isSort = false ;//显示保存取消按钮
            },
           confirmAndCancel(type){
              //排序点击事件
               var  _this = this ;
             //
             if(type=='firm'){
             //保存
                  var sortData =[];
                  if(_this.ArtList!=null && _this.ArtList.length>0 ){

                      for (var item of _this.ArtList) {
                      var params = {
                            id:item.id,
                            sort :item.sortNum
                          };
                         sortData.push(params);
                        }
                         _this.sortList = sortData;
                          _this.sortCategory();//排序
                     }
             }else{
                //取消
                _this.isSort = true ;//显示排序按钮
                 _this.getArtList();//获取数据
             }

            },
            deleteCategory(id){
             //删除分类
             var _this = this ;
             _this.operationArt(1,id);//删除
            },
            //排序素材  sorts {sorts:[{id:1,sort=2},{id:2,sort=3},{id:3,sort=4}]}
            sortCategory(){

                var _this = this ;
                if(_this.sortList==null || this.sortList.length<0){
                    layer.msg('没有修改排序',{
                              offset:['0px','50%']
                      });
                   return;
                }
                var lists ={
                  sorts :_this.sortList
                };
                var listDa = JSON.stringify(lists);
                var params ={
                  sorts :listDa
                }

            var url = Url.FETCH_TYPE_SORT;
               ajaxUtil.doGet(url,params).then((xhr, response) => {
                  var resultVo = response;
                      console.log(xhr);
                   if (resultVo.returnCode =="success") {
                      //刷新数据
                       layer.msg('排序成功！',{
                              offset:['0px','50%']
                            });
                        _this.isSort = true ;//显示排序按钮
                        this.getArtList();//获取数据
                   }else{
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }
               });
               },
            //获取文章列表
            getArtList(){
              var _this = this ;
              var offsets =0 ;
              if(_this.curPage == 0 ||_this.curPage == 1){
                 offsets = 0;//从 0 开始
                }else{
                 offsets =15 * (_this.curPage-1) ;//从 0 开始
                }
              var params = {
                    limit :15,
                    offset :offsets,
                    type_id :_this.type_id
                }
                var indexLayer = layer.load(1, {
                      shade: [0.1,'#fff'] //0.1透明度的白色背景
                    }); //加载
              var url = Url.FETCH_TYPE_LIST;
               ajaxUtil.doGet(url,params).then((xhr, response) => {
                  layer.close(indexLayer); //关闭加载曾层
                  var resultVo = response;
                      console.log(xhr);
                   if (resultVo.returnCode =="success") {
                       _this.ArtList = resultVo.memorys;
                         _this.totalRows = resultVo.totalCount;
                        if(resultVo.totalCount % _this.rows == 0){
                                _this.totalPage = Math.floor(resultVo.totalCount/_this.rows) ;
                        }else{
                               _this.totalPage = Math.floor(resultVo.totalCount/_this.rows) +1;
                        }

                   }else{
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }
               });
            },
             //设置头条 or 取消头条
            setOrCanerHot(id){
            var _this = this ;
             //设为头条
               _this.operationArt(0,id);
            },
            //导入素材
            importMaterial(){
              var _this = this ;
               if(_this.mediaParam !=null && _this.mediaParam.length>0){
                  var mediaParam =[];
                  var mediaJson =[];
                   var paramOne =[];
                   for (var item of _this.mediaParam) {
                     //选中了//mediaParam: "RUdvVkoE2WBq9JNprOnLyc9T9OJ89zUrQ_e5nyFTKKo|1|14528415"
                       mediaJson=item.split("|");
                       paramOne={
                         "media_id":mediaJson[0],
                         "idx":mediaJson[1]
                      };
                       mediaParam.push(paramOne);
                  };
                  var newParams ={
                    items:mediaParam
                  }
                 var paramStr = JSON.stringify(newParams);
                  var params ={
                     items :paramStr,
                     type_id :_this.type_id
                  };
              var url = Url.FETCH_MATlIST_ADD;
                var indexLayer = layer.load(1, {
                      shade: [0.1,'#fff'] //0.1透明度的白色背景
                    }); //加载
               ajaxUtil.doPost(url,params).then((xhr, response) => {
                  layer.close(indexLayer); //关闭加载曾层
                      console.log(xhr);
                   if (response.returnCode =="success") {
                       layer.msg('添加成功！',{
                                  offset:['0px','50%']
                         });
                         _this.mediaParam=[];//赋值从新
                       _this.showBeaMaterial= false;
                       _this.getArtList(); //从新请求
                   }else{
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }
               });


               }else{
                    layer.msg('请选中文章！',{
                              offset:['0px','50%']
                     });
               }
            },
            //对文章的操作,删除  设为头条flag 操作类型 0：设为头条或取消头条，1：删除
            operationArt(type,id){
               var _this = this ;
                  var params ={
                     flag :type,
                     id : id
                  }
              var url = Url.FETCH_TYPE_EDIT;
               ajaxUtil.doGet(url,params).then((xhr, response) => {
                      console.log(xhr);
                   if (response.returnCode =="success") {

                       _this.getArtList(); //从新请求
                      var msg ='删除成功！';
                        if(type == 0){
                            msg ='设置头条成功！';
                       }
                       layer.msg(msg,{
                                  offset:['0px','50%']
                         });

                   }else{
                         if(type == 0){
                            if(response.errCode =="0902"){
                              layer.msg('一个分类最多设置10条头条',{
                                  offset:['0px','50%']
                             });
                            }
                       }
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }
               });
            }


        },
        watch:{
            showBeaMaterial(newVal,oldVal){
                if(newVal == true){
                    this.mediaParam =[]; //附空
                }

            },
             curPage () {
                this.getArtList();
            },
        }
    }
</script>