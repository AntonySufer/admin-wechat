<template>

    <div class="widget">
        <div class="widget-body">
          <!--  <div class="row" >
                <div class="col-xs-12 col-md-12">
                    <div class="form-group">

                        <span class="input-icon icon-right">
                                    <input type="text" class="form-control" style="width:500px;float:left" id="keyWord" placeholder="请输入关键字" v-model="keyWord"/>
                                    <button type="submit" class="btn btn-blue"style="margin-left:8px;" @click="getRelsData(keyWord)">查询</button>
                        </span>
                    </div>
                </div>
            </div>-->

            <div class="row" >
                <div class="col-xs-12 col-md-12">
                    <div class="well with-header  with-footer" style="padding-top:0px;">
                        <table class="table table-hover" >
                            <tbody>
                            <tr v-if="tagRels.length == 0"><td colspan = "3" >暂无数据</td></tr>
                            <tr v-else v-for="tagRel in tagRels">
                                <td width="10%">
                                        <input type="checkbox" :value="tagRel.midAndthumbid" v-model="mediaParam" >
                                </td>
                                <td width="30%">{{tagRel.title}}</td>
                                <td width="30%">{{tagRel.tagName}}</td>

                            </tr>
                            </tbody>
                        </table>
                        <pagination v-if="totalPage>1" :page-per-rows="rows" :total-page="totalPage" :cur-page.sync="curPage" :total-row="totalRows"></pagination>

                    </div>
                </div>
            </div>

        </div>
    </div>

</template>
<script>
import pagination from './common/Pagination.vue'
import * as ajaxUtil from '../util/Ajax.js'
   export default {
       props:{
         mediaParam: []
       },
       components:{
           pagination
       },
       data(){
            return{
               keyWord:null,
                tagRels:[],
                curPage:1, //当前页
                rows:10,
                totalPage:0,
                totalRows:0,
            }
        },
        ready(){
             this.mediaParam =[];
             this.curPage = 1;
             this.getRelsData();
        },
        methods:{
             getRelsData(keyWord){

              let params={};
                if(this.curPage == 0 ||this.curPage == 1){
                 params.offset = 0;//从 0 开始
                }else{
                 params.offset =10 * (this.curPage-1) ;//从 0 开始
                }
                 params.limit = this.rows;
              if(keyWord != null){
                  params.key_word = keyWord;
              }
              let url = Url.FETCH_BEAUTIFUL_MATlIST; //获取文章列表
              var indexLayer = layer.load(1, {
                              shade: [0.1,'#fff'] //0.1透明度的白色背景
                            }); //加载
              ajaxUtil.doGet(url,params).then((xhr,response) => {
                  layer.close(indexLayer); //关闭加载曾层
                      console.log(xhr);
                   if (response.returnCode =="success") {
                         var matelList =response.fodderListVo.item;
                         var newMatList =[];
                    for (var item of matelList) {
                           var childMatItem =item.content.news_item;//每个news_item 里面可能包含多个文章
                             if(childMatItem !=null && childMatItem.length>0){
                                     for (var item of childMatItem) {
                                         var date =new Date().getTime();
                                         //加时间搓，防止两个id一样
                                         item.midAndthumbid =item.media_id+"|"+item.idx+"|"+date;
                                         newMatList.push(item);
                                     }
                             }
                        }
                        this.tagRels = newMatList;
                        this.totalRows = response.fodderListVo.total_count;
                        if(response.fodderListVo.total_count % this.rows == 0){
                                this.totalPage = Math.floor(response.fodderListVo.total_count/this.rows) ;
                        }else{
                               this.totalPage = Math.floor(response.fodderListVo.total_count/this.rows) +1;
                        }

                     } else{
                    console.log("错误信息："+response.errDesc+response.errCode);
                   }

                });
          }
        },
        watch:{
             curPage () {
                this.getRelsData()
             },

        }
   }
</script>
