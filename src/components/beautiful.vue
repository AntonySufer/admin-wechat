<template>
    <div class="continar">
        <div class="page-header position-relative">
            <div class="header-title">
                <h1>美丽秘籍</h1>
            </div>
        </div>


        <div class="page-body">
            <div class="shop_lis" >
                <div class="down_more_div">
                    <div class="searchBtn" style="width:90px" @click="addCategory()">添加分类</div>

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
                    <th class="bor" >分类</th>
                    <th class="bor">排序</th>
                    <th class="bor">文章数</th>
                    <th class="bor">操作</th>
                </tr>
                </thead>
                <tbody>
                       <tr v-if="categoryData.length == 0" ><td colspan = "4" >暂无数据!</td></tr>
                       <tr v-else v-for="(index, item) in categoryData" >
                           <!--类型-->
                            <td class="bor" v-text="item.name" ></td>
                           <!--排序-->
                            <td class="bor" v-if="isSort" v-text="item.sortNum"></td>
                            <td class="bor" v-else><input type="text"  onkeyup="this.value=this.value.replace(/\D/g,'')"  v-model="item.sortNum"/> </td>

                            <td class="bor" v-text="item.fodderCount"></td>
                            <td class="bor">
                                <div class="searchBtn" style="width:90px" @click="goSetArticle(item.id)">设置文章</div>
                                <div class="searchBtn" style="width:90px" @click="updateCategory(item.name,item.id)">编辑</div>
                                <div class="searchBtn" style="width:90px" @click="deleteCategory(item.id)">删除</div>

                            </td>

                        </tr>

                </tbody>

            </table>

        </div>

    </div>

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
    import * as ajaxUtil from '../util/Ajax.js';
    export default{
        data(){
            return {
                msg: '美丽秘籍',
                categoryData :[], //文章
                isShowList:true,//显示list
                sortList :[],//排序的入参
                isSort :true//排序和保存取消按钮切换

            }
        },//计算属性
        computed:{

        },
        components: {

        },//编译
        compiled(){
           this.getBeautiful();//获取数据
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
                  if(_this.categoryData!=null && _this.categoryData.length>0 ){

                      for (var item of _this.categoryData) {
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
                 _this.getBeautiful();//获取数据
             }

            },
            addCategory(){
              //添加分类
             //编辑分类名
              var  _this = this ;
              layer.prompt({
                  formType: 2,
                   maxlength: 10,
                  skin: 'layui-layer-lan',
                  title: '添加分类'
                }, function(value, index, elem){
                   var valueLen =value.replace(/[^\x00-\xff]/g, "**").length;
                   if(valueLen> 10){
                     layer.close(index);
                      var  msglayer =  layer.msg('最多输入5个汉字或10英文字符',{
                              offset:['0px','50%']
                        });
                       return ;
                   }
                     _this.operationCategory(2,'',value,-1);//分类名称flag,id,name,sort，新增默认排序-1
                     layer.close(index);


                });


            },
            updateCategory(title,id){
            //编辑分类名
              var  _this = this ;
              layer.prompt({
                  formType: 2,
                  value: title,
                  maxlength: 10,
                  skin: 'layui-layer-lan',
                  title: '编辑分类'
                }, function(value, index, elem){
                 var valueLen =value.replace(/[^\x00-\xff]/g, "**").length;
                   if(valueLen> 10){
                     layer.close(index);
                      layer.msg('最多输入5个汉字',{
                              offset:['0px','50%']
                        });
                       return ;
                   }
                  _this.operationCategory(0,id,value);//修改分类名称//flag,id,name,sort
                   layer.close(index);
                });

            },
            deleteCategory(id){
             //删除分类
             var _this = this ;
               layer.confirm('确认删除该分类？', {
                      btn: ['删除','取消'] //按钮
                    }, function(){
                      //确认删除
                    _this.operationCategory(1,id,'','');//修改分类名称//flag,id,name,sort
                       layer.closeAll();
                    }, function(){
                });
            },
            //排序素材  sorts {sorts:[{id:1,sort=2},{id:2,sort=3},{id:3,sort=4}]}
            sortCategory(){

                var _this = this ;
                if(_this.sortList==null || this.sortList.length<0){
                    layer.msg('没有修改',{
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

            var url = Url.FETCH_BEAUTIFUL_SORT;
               ajaxUtil.doGet(url,params).then((xhr, response) => {
                  var resultVo = response;
                      console.log(xhr);
                   if (resultVo.returnCode =="success") {
                      //刷新数据
                       layer.msg('排序成功！',{
                              offset:['0px','50%']
                            });
                        _this.isSort = true ;//显示排序按钮
                        this.getBeautiful();//获取数据
                   }else{
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }
               });
               },
           //操作素材  增删查改g 操作标识（必传）：0：编辑 必传名称及id，1：删除 必传id,2:添加，必传名称+序号
           operationCategory(flag,id,name,sort){
              var _this = this ;
              var params = {
                    flag  :flag,
                    id : id,
                    name :name,
                    sort :sort
                }
            var url = Url.FETCH_BEAUTIFUL_EDIT;
               ajaxUtil.doGet(url,params).then((xhr, response) => {

                      console.log(xhr);
                   if (response.returnCode =="success") {
                      var msg ='编辑成功！';
                        if(flag == 1){
                            msg ='删除成功！';
                       }else if(flag == 2){
                            msg ='添加成功！';
                       }
                      var index = layer.msg(msg,{
                              offset:['0px','50%']
                            });

                      //刷新数据
                        _this.getBeautiful();//获取数据

                   }else{
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }
               });
           },

            //获取素材
            getBeautiful(){
                var _this = this ;
              var params = {
                    limit :15,
                    offset :0,
                }
              var url = Url.FETCH_BEAUTIFUL_LIST;
               ajaxUtil.doGet(url,params).then((xhr, response) => {
                  var resultVo = response;
                      console.log(xhr);
                   if (resultVo.returnCode =="success") {

                       _this.categoryData = resultVo.list;
                   }else{
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }
               });
            },
            goSetArticle(typeId){
              //设置文章
              this.$router.go({"name": "setArticle", "params": {"typeId": typeId}});
            }
        },

        watch:{

        }
    }
</script>