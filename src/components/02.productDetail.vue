<template>
  <div>
        <div class="section">
            <div class="location">
                <span>当前位置：</span>
                <a href="/index.html">首页</a> &gt;
                <a href="/goods.html">购物商城</a> &gt;
                <a href="/goods/42/1.html">商品详情</a>
            </div>
        </div>
        <div class="section">
            <div class="wrapper clearfix">
                <div class="wrap-box">
                    <div class="left-925">
                        <div class="goods-box clearfix">
                            <div class="pic-box">
                                 <!-- 放大镜插件 -->
                            <!-- productZoomer设置使用images 而这个变量默认内部是没有数据的 有图片的时候才生产才创建 -->
                            <ProductZoomer 
                            v-if="images.normal_size.length!=0"
                             :base-images="images"
                              :base-zoomer-options="zoomerOptions">
                              </ProductZoomer>
                            </div>
                           
                            <div class="goods-spec">
                                <h1>{{goodsinfo.title}}</h1>
                                <p class="subtitle">{{goodsinfo.sub_title}}</p>
                                <div class="spec-box">
                                    <dl>
                                        <dt>货号</dt>
                                        <dd id="commodityGoodsNo">{{goodsinfo.goods_no}}</dd>
                                    </dl>
                                    <dl>
                                        <dt>市场价</dt>
                                        <dd>
                                            <s id="commodityMarketPrice">¥{{goodsinfo.market_price}}</s>
                                        </dd>
                                    </dl>
                                    <dl>
                                        <dt>销售价</dt>
                                        <dd>
                                            <em id="commoditySellPrice" class="price">¥{{goodsinfo.sell_price}}</em>
                                        </dd>
                                    </dl>
                                </div>
                                <div class="spec-box">
                                    <dl>
                                        <dt>购买数量</dt>
                                        <dd>
                                            <div class="stock-box">
                                                <el-input-number v-model="buyCount" @change="buyCountChange" :min="0" :max="goodsinfo.stock_quantity" size="mini" label="描述文字"></el-input-number>

                                            </div>
                                            <span class="stock-txt">
                                                库存
                                                <em id="commodityStockNum">{{goodsinfo.stock_quantity}}</em>件
                                            </span>
                                        </dd>
                                    </dl>
                                    <dl>
                                        <dd>
                                            <div id="buyButton" class="btn-buy">
                                                  <button onclick="cartAdd(this,'/',1,'/shopping.html');" class="buy">立即购买</button>
                                                <button @click="cartAdd" class="add">加入购物车</button>
                                            </div>
                                        </dd>
                                    </dl>
                                </div>
                            </div>
                        </div>
                        <!-- tab区域 -->
                        <div id="goodsTabs" class="goods-tab bg-wrap">
                            <div id="tabHead" class="tab-head" style="position: static; top: 517px; width: 925px;">
                                <ul>
                                     <Affix>
                                          <li>
                                        <a @click="showDiscuss=false" :class="{selected:!showDiscuss}" href="javascript:;" >商品介绍</a>
                                    </li>
                                    <li>
                                        <a @click="showDiscuss=true" :class="{selected:showDiscuss}" href="javascript:;">商品评论</a>
                                    </li>
                            <!-- <span class="demo-affix">Fixed at the top</span> -->
                                    </Affix>
                                   
                                </ul>
                            </div>
                            <!-- 内容区域  -->
                            <div class="tab-content entry" v-show="!showDiscuss" v-html="goodsinfo.content">
                                内容
                            </div>

                            <!-- 评论区域 -->
                            <div class="tab-content" v-show="showDiscuss">
                                <div class="comment-box">
                                    <div id="commentForm" name="commentForm"
                                        class="form-box">
                                        <div class="avatar-box">
                                            <i class="iconfont icon-user-full"></i>
                                        </div>
                                        <div class="conn-box">
                                            <div class="editor">
                                                <textarea v-model.trim="commentInfo" id="txtContent" name="txtContent" sucmsg=" " datatype="*10-1000" nullmsg="请填写评论内容！"></textarea>
                                                <span class="Validform_checktip"></span>
                                            </div>
                                            <div class="subcon">
                                                <input @click="submitCommit" id="btnSubmit" name="submit" type="submit" value="提交评论" class="submit">
                                                <span class="Validform_checktip"></span>
                                            </div>
                                        </div>
                                    </div>
                                    <ul id="commentList" class="list-box">
                                        <p v-show="comments.length==0" style="margin: 5px 0px 15px 69px; line-height: 42px; text-align: center; border: 1px solid rgb(247, 247, 247);">暂无评论，快来抢沙发吧！</p>
                                        <li v-for="(item, index) in comments" :key="item.id">
                                            <div class="avatar-box">
                                                <i class="iconfont icon-user-full"></i>
                                            </div>
                                            <div class="inner-box">
                                                <div class="info">
                                                   <span>{{item.user_name}}</span>
                                                    <span>{{item.reply_time | filterDate}}
                                                        <!-- <Rate v-model="" /> -->
                                                    </span>
                                                </div>
                                                <p>{{item.content}}</p>

                                            </div>
                                        </li>
                                        <li>
                                            <div class="avatar-box">
                                                <i class="iconfont icon-user-full"></i>
                                            </div>
                                            <div class="inner-box">
                                                <div class="info">
                                                    <span>匿名用户</span>
                                                    <span>2017/10/23 14:59:36</span>
                                                </div>
                                                <p>很清晰调动单很清晰调动单</p>
                                            </div>
                                        </li>
                                    </ul>
                                    <div class="page-box" style="margin: 5px 0px 0px 62px;">
                                        <Page :total="totalCount"
                                         show-elevator show-sizer placement="top"
                                          :page-size-opts="[5, 6,10, 13, 26]"
                                           @on-change="pageChange"
                                            @on-page-size-change="pageSizeChange" />

                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="left-220">
                        <div class="bg-wrap nobg">
                            <div class="sidebar-box">
                                <h4>推荐商品</h4>
                                <ul class="side-img-list">
                                    <li v-for="(item, index) in hotgoodslist" :key="item.id">
                                        <div class="img-box">
                                            <!-- <a href="#/site/goodsinfo/90" class=""> -->
                                            <router-link :to="'/detail/'+item.id">
                                                <img :src="item.img_url">
                                            </router-link>
                                            <!-- </a> -->
                                        </div>
                                        <div class="txt-box">
                                            <!-- <a href="#/site/goodsinfo/90" class="">{{item.title}}</a> -->
                                            <router-link :to="'/detail/'+item.id">{{item.title}}</router-link>
                                            <span>{{item.add_time | filterDate}}</span>
                                        </div>
                                    </li>
                                 
                                </ul>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
        </div>
         <!-- 回到顶部 iview的自定义组件 -->
        <BackTop :height="100" :bottom="200">
            <div class="top">返回顶端</div>
        </BackTop>
               <!-- 移动的小图片 -->
        <img v-if="imglist.length!=0" class="moveImg" :src="imglist[0].original_path" alt="">
    </div>
</template>

<script>

// 导入jq
import $ from "jquery";

// 转包 导包 放大镜
import ProductZoomer from 'vue-product-zoomer';


export default {
    name:"detail",
    data:function(){
        return{
            productId:undefined,
            goodsinfo:{},//商品信息
            hotgoodslist:[],//热卖信息
            imglist:[],//图片列表
            buyCount:0, //购买数量
            showDiscuss:false,//是否显示评论
                        // 放大镜设置
                zoomerOptions: {
                    'zoomFactor': 2, //放大的倍数
                    'pane': 'container-round',  //小放大镜
                    'hoverDelay': 300,
                    'namespace': 'zoomer',
                    'move_by_click':true,  //点击切换
                    'scroll_items': 7,
                    'choosed_thumb_border_color': "#dd2c00" //框的颜色
                },
      // 轮播图用的图片 默认的数据格式 不支持
      // 这里的数据 需要在接口调用完毕之后 才能够获取
                    images:{
                        normal_size:[
                            
                        ]
                    },
                     // 页码
                pageNum: 1,
                // 页容量
                pageSize: 10,
                // 评论数据
                comments: [],
                // 总条数
                totalCount: 0,
                // 评论内容 双向数据绑定
                commentInfo: ""


        };
    },
        // 点击加1
    methods:{
        buyCountChange:function(){
            console.log('我变了');
        },

          // 这里可以写抽取的方法
            getProductDetail() {
             // 保存id
                this.productId = this.$route.params.id;
                // ajax获取数据
                // 数据回来之后渲染到页面上
                this.$axios
                .get(
                    `/site/goods/getgoodsinfo/${this.productId}`)
                .then(response=>{
                    console.log(response);
                    this.goodsinfo = response.data.message.goodsinfo;
                    this.hotgoodslist = response.data.message.hotgoodslist;
                    this.imglist = response.data.message.imglist;

                    // 处理 放大镜数据
                let temArr = [];
                // 循环处理数据
                this.imglist.forEach((v,i)=>{
                    temArr.push({
                        id:v.id,
                        url:v.original_path
                    })
                })
                // 临时数组
                this.images.normal_size = temArr
                });
                // 注册放大镜
                    },

              //获取评论的方法
        getComments() {
        this.$axios
            .get(
            `/site/comment/getbypage/goods/${this.productId}?pageIndex=${this.pageNum}&pageSize=${this.pageSize}`)
            .then(response => {
            // console.log(response);
            this.comments = response.data.message;
            this.totalCount = response.data.totalcount;
            });
        },
         // 页码改变 会给我页码
            pageChange(page) {
            console.log(page);
            this.pageNum = page;
            // 页码改变时 重新获取数据即可
            this.getComments();
            },
            // 页容量改变 自动触发 页码改变 把页码改为1
            // 如果当前页码就是1 不会触发 pageChange
            // 会给我页容量
            pageSizeChange(size) {
                // alert('2');
            console.log(size);
            this.pageSize = size;
            // 如果就是第一页 重新获取数据
            if (this.pageNum == 1) {
                this.getComments();
            }
            },
            //发表评论点击提交
            submitCommit(){
                if(this.commentInfo.length==0){
                    // 直接弹框
                    this.$Message.error('大哥,随便写点金玉良言呗!');
                    return;
                }
                //调用接口
                this.$axios.post(`/site/validate/comment/post/goods/${
                    this.productId
                    }`,
                    {
                        commenttxt:this.commentInfo
                    }
                    )
                .then(response=>{
                    //发表成功之后 局部刷新 调用函数
                    console.log(response);
                    // 提示用户成功了
                    this.$Message.success(response.data.message);
                    // 发表成功 局部刷新调用局部刷新的玩意
                    this.getComments();
                    // 
                    this.commentInfo ="";
                })
            },
            // 使用jquery来加入购物车
            cartAdd(){
                //为0提示
                if(this.buyCount==0){
                    this.$Message.error("请多买点东西");
                    return;
                }

                // 获取加入购物车位置
                let cartOffset = $('.add').offset();
                // console.log(cartOffset);
                // 获取购物车的位置
                let targetOffset =$('.icon-cart').offset();
                // console.log(targetOffset);
                // 使用动画的方式 移动图片
                // 移动到按钮位置显示出来 动画移动到目标位置
                $(".moveImg").stop().show().addClass('move').css(cartOffset).animate(targetOffset,2000,function(){
                    $(this).hide().removeClass('move');
                });
                // 动画完结以后
                // 隐藏图片
                // 增加购物车中的显示内容
                this.$store.commit("addGoods",{goodId:this.productId,goodNum:this.buyCount});
            },

    },
    // 生命周期函数
    // 当前这个Vue组件还还没有实例化出来 那些data methods 都是没有的
    created(){
       //   调用 获取数据的函数
    this.getProductDetail();
    // 获取评论
    this.getComments();
    },
      // 观察数据改变
    watch: {
        $route(val, oldVal) {
        //   console.log(val);
        //   console.log(oldVal);
        //   console.log('改变啦');
        // 认为让他 强制生成 v-if 数组长度
        // 数组长度为0 直接销毁
        this.images.normal_size = [];

        // 重新调用接口 获取数据 渲染页面
        // 回调函数中重新复制 再次 生成
        this.getProductDetail();
        }
    }
   

    
}
</script>

<style lang="less">
// lang=less 让vue 使用less 来解析 这里的样式
/* 设置 内容区域 图片样式 */
.tab-content img {
  width: 100%;
  /* 去除图片底部的 间隙 */
  display: block;
}
/* iview的回到顶部 */
.top {
  padding: 10px;
  background: rgba(0, 153, 229, 0.7);
  color: #fff;
  text-align: center;
  border-radius: 2px;
}
/* 放大镜相关样式 */
.pic-box {
  width: 395px;
  .control-box .thumb-list {
    display: flex;
    justify-content: center;
    img {
      width: 80px;
      height: 80px;
      margin: 2px;
    }
  }
  .control {
    //   text-align:center;
    display: flex;
    // 主轴
    justify-content: center;
    // 纵轴 副轴 侧轴
    align-items: center;
  }
}

// 移动图片的样式
.moveImg {
  position: absolute;
  width: 50px;
//   display: none;
  // top:0;
  // left:0;
}
.moveImg.move {
  transition: transform 1s, opacity 1s;
  opacity: 0.5;
  transform: scale(1) rotate(7200deg);
}
</style>


