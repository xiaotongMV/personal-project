<template>
  <div class="pos">
      <el-row>
        <el-col :span="7" class="pos-order" id="order-list">
          <el-tabs>
            <el-tab-pane label="点餐">
              <!-- show-summary -->
              <el-table :data="tableData" border fixed style="width:100%;">
                <el-table-column prop="goodsName" label="商品名称" class="cenTer"></el-table-column>
                <el-table-column prop="count" label="数量" width="70"></el-table-column>
                <el-table-column prop="price" label="金额" width="70"></el-table-column>
                <el-table-column  label="操作" width="100" fixed="right">
                  <template slot-scope="scope">
                    <el-button type="text" size="small" @click="delSingleGoods(scope.row)">删除</el-button>
                    <el-button type="text" size="small" @click="addOrderList(scope.row)">增加</el-button>
                  </template>
                </el-table-column>
              </el-table>

              <div class="totalDiv">
                <small>数量：</small>{{totalCount}} &nbsp;&nbsp;&nbsp;&nbsp;<small>金额：</small>{{totalMoney}}元
              </div>

              <div class="marginTop">
                <el-button type="warning">挂单</el-button>
                <el-button type="danger" @click="delAllGoods">删除</el-button>
                <el-button type="success" @click="checkout">结账</el-button>
              </div>
            </el-tab-pane>
            <el-tab-pane label="挂单">
              挂单
            </el-tab-pane>
            <el-tab-pane label="外卖">
              外卖
            </el-tab-pane>
          </el-tabs>
        </el-col>
        <!-- 内容栏 -->
        <el-col :span="17">
          <div class="often-goods">
            <div class="title">常用商品</div>
            <div class="often-goods-list">
              <ul>
                <li v-for="goods in oftnGoods" @click="addOrderList(goods)">
                  <span>{{goods.goodsName}}</span>
                  <span class="o-prios">¥{{goods.price}}元</span>
                </li>
              </ul>
            </div>
          </div>

          <div class="goods-type">
            <el-tabs>
              <el-tab-pane label="汉堡">
                <div>
                  <ul class='cookList'>
                    <li v-for="goods in type0Goods" @click="addOrderList(goods)">
                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label="小食">
                <div>
                  <ul class='cookList'>
                    <li v-for="goods in type1Goods" @click="addOrderList(goods)">
                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label="饮料">
                <div>
                  <ul class='cookList'>
                    <li v-for="goods in type2Goods" @click="addOrderList(goods)">
                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
              <el-tab-pane label="套餐">
                <div>
                  <ul class='cookList'>
                    <li v-for="goods in type3Goods" @click="addOrderList(goods)">
                        <span class="foodImg"><img :src="goods.goodsImg" width="100%"></span>
                        <span class="foodName">{{goods.goodsName}}</span>
                        <span class="foodPrice">￥{{goods.price}}元</span>
                    </li>
                  </ul>
                </div>
              </el-tab-pane>
            </el-tabs>
          </div>
        </el-col>
      </el-row>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name:'pos',
  data(){
    return{
       tableData: [],
        oftnGoods:[],
        type0Goods:[],
        type1Goods:[],
        type2Goods:[],
        type3Goods:[],
        totalMoney:0,
        totalCount:0
    }
  },
  created:function(){
    axios.get('http://jspang.com/DemoApi/oftenGoods.php')
    .then(reponse=>{
      console.log(reponse)
      this.oftnGoods=reponse.data;
    })
    .catch(error=>{
      alert('网络错误，不能访问')
    })

    axios.get('http://jspang.com/DemoApi/typeGoods.php')
    .then(reponse=>{
      this.type0Goods=reponse.data[0];
      this.type1Goods=reponse.data[1];
      this.type2Goods=reponse.data[2];
      this.type3Goods=reponse.data[3];
    })
    .catch(error=>{
      alert('网络错误，不能访问')
    })
  },
  //解决100%高度问题的钩子函数
  mounted:function(){
    var orderHeiht=document.body.clientHeight;
    document.getElementById('order-list').style.height=orderHeiht+'px';
  },
  methods:{
    addOrderList(goods){
      this.totalMoney = 0;
      this.totalCount = 0;
      //商品是否已经存在于订单列表中
      let isHave = false;
      for(let i=0;i<this.tableData.length;i++){
        console.log(this.tableData[i].goodsId)
        if(this.tableData[i].goodsId==goods.goodsId){
          isHave = true;
        }
      }
      //根据判断的值编写业务逻辑
      if(isHave){
        //改变列表中商品数量
        let arr = this.tableData.filter(o=>o.goodsId == goods.goodsId);
        arr[0].count++;
      }else{
        //不存在就推入数组
        let newGoods = {
          goodsId:goods.goodsId,
          goodsName:goods.goodsName,
          price:goods.price,
          count:1
        }
        this.tableData.push(newGoods);
      }
      this.getAllMoney();
    },
    //删除单个商品
    delSingleGoods(goods){
      this.tableData = this.tableData.filter(o=>o.goodsId != goods.goodsId);
      this.getAllMoney();
    },
    //全部删除
    delAllGoods(){
      this.tableData = [];
      this.totalMoney = 0;
      this.totalCount = 0;
    },
    //模拟结账
    checkout(){
      if(this.totalCount!=0){
        this.tableData=[];
        this.totalMoney = 0;
        this.totalCount = 0;
        this.$message({
          message:'结账成功',
          type:'success',
        })
      }else{
        this.$message.error('亲，账单是空的')
      }
    },
    //汇总数量金额
    getAllMoney(){
      this.totalMoney = 0;
      this.totalCount = 0;
      if(this.tableData){
         //进行数量和价格的汇总计算
        this.tableData.forEach((element)=>{
          this.totalCount+=element.count;
          this.totalMoney=this.totalMoney+(element.price*element.count)
        })
      }
    }
  },
  
}
</script>
<style scoped>
.pos-order{
  background-color: #f9fafc;
  border-right: 1px solid #c0ccda;
}
.marginTop{
  margin-top: 25px;
}
.title{
  height: 20px;
  border-bottom:1px solid #d3dce6;
  background-color: #f9fafc;
  padding: 10px;
  text-align: left;
}
.often-goods-list ul li{
  list-style: none;
  float: left;
  border: 1px solid #76a0f5;
  padding: 10px;
  margin: 10px;
  background-color: #fff;
  cursor: pointer;
}
.o-prios{
  color: #58b7ff;
}
.goods-type{
  clear: both;
}
/* 无序列表样式 */
.cookList li{
    list-style: none;
    width:23%;
    border:1px solid #E5E9F2;
    height: auot;
    overflow: hidden;
    background-color:#fff;
    padding: 2px;
    float:left;
    margin: 2px;
    cursor: pointer;
}
.cookList li span{
    
    display: block;
    float:left;
}
.foodImg{
    width: 40%;
}
.foodName{
    font-size: 16px;
    padding-left: 10px;
    color:brown;
}
.foodPrice{
    font-size: 16px;
    padding-left: 10px;
    padding-top:10px;
}
.totalDiv{
  background-color: #fff;
  padding: 10px;
  border-bottom: 1px solid #d4dfdf;
}
</style>
