<!--  -->
<template>
  <section class="deal">
    <section class="top">
      <h3>
        订单情况
        <span>本日订单内容已更新</span>
      </h3>
      <section class="list">
        <section>
          <i class="el-icon-s-finance"></i>
          <em>订单总数</em>
          <span>{{count}}</span>
        </section>
        <section>
          <i class="el-icon-s-shop"></i>
          <em>待收货</em>
          <span>{{incomplete}}</span>
        </section>
        <section>
          <i class="el-icon-s-goods"></i>
          <em>已收货</em>
          <span>{{done}}</span>
        </section>
<!--        <section>-->
<!--          <i class="el-icon-s-order"></i>-->
<!--          <em>已删除</em>-->
<!--          <span>{{del}}</span>-->
<!--        </section>-->
      </section>
    </section>
    <section class="bottom">
      <h3>
        订单管理
        <span>订单列表管理今日已完成更新</span>
      </h3>
      <el-table :data="listArr" border style="width: 100%" >
        <el-table-column fixed prop="ProductNum" label="产品编号" width="100"></el-table-column>
        <el-table-column prop="JoiningTime" label="日期" width="180"></el-table-column>
        <el-table-column prop="Region" label="地区" width="60"></el-table-column>
        <el-table-column prop="OriginalPrice" label="价格" width="120"></el-table-column>
        <el-table-column prop="ProductName" label="商品名称" width="300"></el-table-column>
        <el-table-column prop="Status" label="订单状态" width="120"></el-table-column>
        <el-table-column fixed="right" label="操作" width="100">
          <template slot-scope="scope">
<!--            <el-button  type="text" size="small">编辑</el-button>-->
<!--              <el-button type="message"  @click="open" style="font-size: 12px">编辑</el-button>-->
            <el-button @click="handleClick(scope.row)" type="text" size="small">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
       <div class="page">
           <el-pagination
                   @current-change="changePage()"
                   @prev-click="previous"
                   @next-click="nextPage"
                   background
                   layout="prev, pager, next"
                   :current-page.sync ="pageNum"
                   :total=count
                    :page-size="5">
           </el-pagination>
       </div>
    </section>
  </section>

</template>

<script>
export default {
    methods: {
        //删除订单
      handleClick(row) {
        // console.log(row);
        this.axios({
            url:'/api/product/deleteProduct',
            method:'post',
            data:{
                product_num:row.ProductNum
            }
        })
          .then(()=>{
              this.getData();
          })
      },
        //获取数据
        getData(){
            this.axios({
                url:'/api/product/list',
                method:'get',
                params:{
                    _page:this.pageNum,
                    _limit:5
                }
            }).then(res=>{
                // console.log(res)
                this.done = 0;
                this.incomplete = 0;
                this.listArr = res.data.data;
                this.count = res.data.count;
                this.listArr.map(item=>{
                    return item.Status === 1 ? item.Status = '完成' : item.Status = '进行中'
                })
                //时间戳转日期
               this.listArr.forEach(item=>{
                   item.Status === '完成' ? this.done++ : this.incomplete++
                   item.JoiningTime = new Date(parseInt(item.JoiningTime)).toLocaleString().replace(/:\d{1,2}$/,' ');

               })
            })
        },
        //点击选择页渲染
        changePage(){
            // console.log(this.pageNum);
            this.getData()
        },
        //点击上一页渲染数据
        previous(){
          this.pageNum--;
            this.getData()
        },
        //点击下一页渲染数据
        nextPage(){
            this.pageNum++;
            this.getData()
        },
    },

    //页面初始加载一次
    mounted() {
        this.getData()
    },
    data() {
      return {
          listArr:[],
          pageNum:1,
          count:0,//总条数
          incomplete:0,//未完成的订单条数
          done:0,//已收货订单条数
      }
    },

  }
</script>
<style lang='less' scoped>
    .page{
        display: flex;
        justify-content: flex-end;
        margin-top: 5px;
    }
.deal {
  .top {
    height: 140px;
    max-width: 1040px;
    margin: 0 auto;
    background-color: #fff;
    margin-bottom: 30px;
    border-radius: 5px;
    padding: 20px;
    box-sizing: border-box;
    h3 {
      font-weight: 600;
      span {
        font-size: 12px;
        color: #ccc;
      }
    }
    .list {
      height: 50px;
      // margin-top: 20px;
      padding: 20px 50px;
      display: flex;
      justify-content: space-between;
      align-content: center;
      section {
        height: 50px;
        width: 200px;
        display: flex;
        align-items: center;
        i {
          font-size: 35px;
        }
        em {
          font-size: 14px;
          display: inline-block;
          width: 70px;
          // line-height: 50px;
        }
        span {
          font-size: 25px;
        }
        &:nth-child(1) {
          color: rgb(248, 74, 163);
        }
        &:nth-child(2) {
          color: rgb(139, 42, 191);
        }
        &:nth-child(3) {
          color: rgb(71, 107, 249);
        }
        &:nth-child(4) {
          color: rgb(249, 191, 34);
          border: none;
        }
      }
    }
  }
  .bottom {
    /*min-width: 700px;*/
      margin: 0 auto;
      max-width: 1000px;
    background-color: #fff;
    border-radius: 5px;
    padding: 20px;
    h3 {
      font-weight: 600;
      margin-bottom: 20px;
      span {
        font-size: 12px;
        color: #ccc;
      }
    }
  }
}
</style>