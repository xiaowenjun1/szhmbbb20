<template>
    <div>
            <!-- v-loading="loading"
        element-loading-text="拼命加载中"
        element-loading-spinner="el=icon-loading"
        element-loading-background="yellowgreen" -->

         <div class="section">
            <div class="location">
                <span>当前位置：</span>
                <a href="/index.html">首页</a> &gt;
                <a href="/cart.html">购物车</a>
            </div>
        </div>

        <div class="section">
            <div class="wrapper">
                <div class="bg-wrap">
                    <!--购物车头部-->
                    <div class="cart-head clearfix">
                        <h2>
                            <i class="iconfont icon-cart"></i>我的购物车</h2>
                        <div class="cart-setp">
                            <ul>
                                <li class="first active">
                                    <div class="progress">
                                        <span>1</span>
                                        放进购物车
                                    </div>
                                </li>
                                <li>
                                    <div class="progress">
                                        <span>2</span>
                                        填写订单信息
                                    </div>
                                </li>
                                <li class="last">
                                    <div class="progress">
                                        <span>3</span>
                                        支付/确认订单
                                    </div>
                                </li>
                            </ul>
                        </div>
                    </div>
                    <!--购物车头部-->
                    <!--商品列表-->
                    <div class="cart-box">
                        <input id="jsondata" name="jsondata" type="hidden">
                        <table width="100%" align="center" class="cart-table" border="0" cellspacing="0" cellpadding="8">
                            <tbody>
                                <tr>
                                    <th width="48" align="center">
                                        <a>选择</a>
                                    </th>
                                    <th align="left" colspan="2">商品信息</th>
                                    <th width="84" align="left">单价</th>
                                    <th width="104" align="center">数量</th>
                                    <th width="104" align="left">金额(元)</th>
                                    <th width="54" align="center">操作</th>
                                </tr>
                                <!-- 没有商品时显示 -->
                                <tr v-if="message.length==0">
                                    <td colspan="10">
                                        <div class="msg-tips">
                                            <div class="icon warning">
                                                <i class="iconfont icon-tip"></i>
                                            </div>
                                            <div class="info">
                                                <strong>购物车没有商品！</strong>
                                                <p>您的购物车为空，
                                                    <a href="/index.html">马上去购物</a>吧！</p>
                                            </div>
                                        </div>
                                    </td>
                                </tr>
                                <tr v-for="(item, index) in message" :key="item.id">
                                    <!-- 开关 -->
                                    <td>
                                             <el-switch v-model="item.selected" active-color="black" inactive-color="hotpink">
                                        </el-switch>
                                    </td>
                                    <td>
                                        <img :src="item.img_url" alt="" style="width:80px">
                                       
                                    </td>
                                    <td> {{item.title}}</td>
                                    <td>{{item.sell_price}}</td>
                                    <td>
                                        <!-- 直接min='0' 解析的是 字符串 -->
                                        <!-- 如果 要保留 原始的形参 ,额外的增加一个自定义的实参
                                            $event 保留事件参数
                                                传送门
                                            在后面增加自定义的参数
                                         -->
                                        <el-input-number size="mini" :min='0' :max="item.max" v-model="item.buycount" @change="numChange($event,item.id)"></el-input-number>
                                    </td>
                                    <td>{{item.buycount*item.sell_price}}</td>
                                    <td>
                                        <el-button @click="delOne(item.id)" type="danger" icon="el-icon-delete" circle></el-button>
                                    </td>
                                </tr>
                                <tr>
                                    <th align="right" colspan="8">
                                        已选择商品
                                        <b class="red" id="totalQuantity">{{totalCount}}</b> 件 &nbsp;&nbsp;&nbsp; 商品总金额（不含运费）：
                                        <span class="red">￥</span>
                                        <b class="red" id="totalAmount">{{totalPrice}}</b>元
                                    </th>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                    <!--/商品列表-->
                    <!--购物车底部-->
                    <div class="cart-foot clearfix">
                        <div class="right-box">
                            <router-link to="/index">
                            <button class="button">
                                    继续购物
                                </button>
                            </router-link>
                                
                            <button class="submit" @click="checkAndSubmit">立即结算</button>
                        </div>
                    </div>
                    <!--购物车底部-->
                </div>
            </div>
        </div>
    </div>
</template>
<script>

export default {
    // 在开发者工具中国看到组件的名字
    name:"shoppingCart",
    data:function(){
        return{
            message:[]
        };
    },
    // 生命周期函数(钩子函数)
    created() {
        // 准备数据 id1,id2,id3
        // console.log(this.$store.state.creatDate);
        // 定义变量暂存一个
        let cartDate = this.$store.state.cartDate;
        // 定义拼接的数据
        let ids = "";
        for(const key in cartDate){
            ids += key;
            ids +=","
        }
        // 去掉最后多余的逗号 -1代表最后一个不要
        ids = ids.slice(0,-1);
        console.log(ids);
            // this.loading = true;

        //调用接口获取数
        this.$axios.get(`/site/comment/getshopcargoods/${ids}`)
        .then(response=>{
            // this.loading = false;
            // console.log(response);
            // 自己拼接
            response.data.message.forEach(v => {
                //获取Vuex中的 id 对应的值
                v.buycount = cartDate[v.id];
                // 购物车的商品默认被选中
                v.selected = true;
                this.$axios.get(`/site/goods/getgoodsinfo/${v.id}`).then(resSon=>{
                    console.log(resSon);
                    v.max = resSon.data.message.goodsinfo.stock_quantity;
                })
            });
            // 再赋值给message
            this.message = response.data.message;
        });


                   
    },
    // 计算属性
    computed:{
        // 总金额
        totalPrice(){
            // 选中的
            let totalPrice=0;
            // 购买个数乘以价格
            this.message.forEach(v=>{
                if(v.selected==true){
                    // 累加 返回
                    totalPrice += v.sell_price*v.buycount;
                }
            });
            return totalPrice;
        },
          // 总个数
        totalCount() {
        // 被选中的
        let totalCount = 0;
        // 购买个数*价格
        this.message.forEach(v => {
            if (v.selected == true) {
            // 累加
            // 返回
            // 变为了字符串拼接 转化为整数即可
            totalCount += parseInt(v.buycount);
            }
        })
        return totalCount;
        }

    },
    // 方法
    methods:{
        numChange(num, id) {
        //   console.log(num,id);
        this.message.forEach(v=>{
            if(v.id==id){
                if(num>v.max){
                    num = v.max;
                }
            }
        })
        // 调用仓库的方法 (提交载荷)
        this.$store.commit("updateGoodsNum", {
            goodId: id,
            goodNum: num
        });
        },
       // 删除数据
        delOne(id) {
        //   console.log(id);
        // 提交载荷 这里是删除 Vuex中的
        this.$store.commit("deleteGoods", id);
        // 页面中的 并没有删除
        // this.message
        this.message.forEach((v, index) => {
            if (v.id == id) {
            this.message.splice(index, 1);
            }
        });
        },
        // 验证登录 跳转登录页
        checkAndSubmit(){
            // 判断是否选择商品
            if(this.totalPrice==0){
                this.$message.error('请买点商品再结算');
                return;
            }
            // 到这里 直接准备去订单页面
            // 获取选中的id
            let ids = '';
            this.message.forEach(v=>{
                // 选中的 才累加
                if(v.selected==true){
                    ids+=v.id;
                    ids+=",";
                }
            })
            // 去掉最后的
            ids =ids.slice(0,-1);
            this.$router.push(`/order/${ids}`);
        }
    }
}
</script>
