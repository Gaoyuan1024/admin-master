<template>
  <div >
    <div class="detail-container">
      <el-page-header @back="goBack" :content="this.$route.query.author + '详情页面'"></el-page-header>
      <el-card class="box-card">
        <div>
          <el-descriptions title="带边框列表" :column="3" size="medium" border>
            <template slot="extra">
              <el-button type="primary" size="small">操作</el-button>
            </template>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-user"></i>
                用户名
              </template>
              {{from.author}}
            </el-descriptions-item>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-mobile-phone"></i>
                标题
              </template>
              {{from.title}}
            </el-descriptions-item>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-location-outline"></i>
                点击量
              </template>
              {{from.pageViews}}
            </el-descriptions-item>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-tickets"></i>
                状态
              </template>
              <el-tag size="small">{{from.status}}</el-tag>
            </el-descriptions-item>
            <el-descriptions-item>
              <template slot="label">
                <i class="el-icon-office-building"></i>
                时间
              </template>
              {{from.datetime}}
            </el-descriptions-item>
          </el-descriptions>
          <div class="margin-top el-descriptions">
            <div class="el-descriptions__header">
              <div class="el-descriptions__title">带边框列表</div>
            </div>
            <div class="el-descriptions__body">
              <el-table
                :data="tableData"
                height="250"
                border
                style="width: 100%">
                <el-table-column
                  prop="date"
                  label="日期"
                  >
                </el-table-column>
                <el-table-column
                  prop="name"
                  label="姓名"
                  >
                </el-table-column>
                <el-table-column
                  prop="address"
                  label="地址">
                </el-table-column>
              </el-table>
            </div>
          </div>




        </div>
      </el-card>



    </div>
 </div>
</template>
<script>
  export default {
    name: "Table-Detail",
    data() {
      return {
        from:this.$route.query,
       
        tableData: [{
          date: '2016-05-03',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-02',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-04',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-01',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-08',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-06',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }, {
          date: '2016-05-07',
          name: '王小虎',
          address: '上海市普陀区金沙江路 1518 弄'
        }]

      }
    },

    created() {

    },
    watch: {

    },
    methods: {
      goBack(){
        this.$router.go(-1)
      }
    },
  }
</script>
<style lang="scss">
.detail-container{
  padding: 20px;
  .el-page-header {
    margin: 0 0 20px 0;
  }
  .margin-top {
      margin-top: 20px;
  }

}
</style>