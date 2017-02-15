<template>
    <div class="continar">
        <div class="page-header position-relative">
            <div class="header-title">
                <h1>设置菜单</h1>
            </div>
        </div>
        <div class="contbody">
          <textarea rows="10" cols="20" v-model="content"  class="form-control menunBut" ></textarea>
            <div class="searchBtn" style="width:90px" @click="commitBut()">设置</div>
            <h4 >结果显示：</h4>
            <textarea rows="10" cols="20" v-model="resultVo" style="float:left;"  class="form-control menunBut" ></textarea>
        </div>

    </div>

</template>
<style>
    body{
        background-color:#ff0000;
    }

    .contbody{
      width:100%;
      background-color:#fff;
      height:500px;
      float:left;
    }

    .menunBut{
       width:700px;
    }
     .commitBut{
       margin-left:200px;
    }
</style>
<script>
 import * as ajaxUtil from '../util/Ajax.js';
    export default{
        data(){
            return{
                content:'',
                resultVo :"", //显示结果
                temJson : ""
            }
        },ready(){

        },
         methods : {
            commitBut(){
              //提交
             var  _this = this ;
              var params ={
                  menu :_this.content
                }


            var url = Url.FETCH_MENU_SET;
               ajaxUtil.doPost(url,params).then((xhr, response) => {
                  var resultVo = response;
                     _this.resultVo = JSON.stringify(response) ;
                      console.log(xhr);
                   if (resultVo.returnCode =="success") {
                      //刷新数据
                       layer.msg('提交成功！',{
                              offset:['0px','50%']
                       });
                       _this.content ="";

                   }else{
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }
               });
            }
         }
    }
</script>
