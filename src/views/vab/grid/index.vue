<template>
  <div class="table-container">
    <vab-query-form>
      <vab-query-form-left-panel>
        <el-button icon="el-icon-plus" type="primary" @click="handleAdd">
          添加
        </el-button>
        <el-button icon="el-icon-delete" type="danger" @click="handleDelete">
          删除
        </el-button>
        <!-- <el-button type="primary" @click="testMessage">baseMessage</el-button>
        <el-button type="primary" @click="testALert">baseAlert</el-button>
        <el-button type="primary" @click="testConfirm">baseConfirm</el-button>
        <el-button type="primary" @click="testNotify">baseNotify</el-button> -->
      </vab-query-form-left-panel>
      <vab-query-form-right-panel>
        <el-form
          ref="form"
          :model="queryForm"
          :inline="true"
          @submit.native.prevent
        >
          <el-form-item>
            <el-input v-model="queryForm.title" placeholder="标题" />
          </el-form-item>
          <el-form-item>
            <el-button
              icon="el-icon-search"
              type="primary"
              native-type="submit"
              @click="handleQuery"
            >
              查询
            </el-button>
          </el-form-item>
        </el-form>
      </vab-query-form-right-panel>
    </vab-query-form>

    <u-table
      pagination-show
      :current-page="queryForm.pageNo"
      :layout="layout"
      :page-size="queryForm.pageSize"
      :page-sizes="pageSizes"
      :total="total"     
      ref="tableSort"
      v-loading="listLoading"
      :data="list"
      :size="'mini'"
      :border="true"
      :row-height="40"
      :element-loading-text="elementLoadingText"
      :height="height"
      row-key="id"
      record-table-select
      use-virtual
      big-data-checkbox
      @selection-change="setSelectRows"
      @table-select-change="tableSelectChange"
      @sort-change="tableSortChange"
      @handlePageSize="handlePageSize"
    >
      <u-table-column
        show-overflow-tooltip
        type="selection"
        width="55"
        fixed
        reserve-selection
      ></u-table-column>
      <u-table-column show-overflow-tooltip label="序号" width="95">
        <template #default="scope">
          {{ scope.$index + 1 }}
        </template>
      </u-table-column>
      <u-table-column
        show-overflow-tooltip
        prop="id"
        label="ID"
      ></u-table-column>
      <u-table-column
        show-overflow-tooltip
        prop="title"
        label="标题"
      ></u-table-column>
      <u-table-column
        show-overflow-tooltip
        label="作者"
        prop="author"
      ></u-table-column>
      <el-table-column show-overflow-tooltip label="头像">
        <template #default="{ row }">
          <el-image
            v-if="imgShow"
            :preview-src-list="imageList"
            :src="row.img"
          ></el-image>
        </template>
      </el-table-column>
      <u-table-column
        show-overflow-tooltip
        label="点击量"
        prop="pageViews"
        sortable
      ></u-table-column>
      <u-table-column show-overflow-tooltip label="状态">
        <template #default="{ row }">
          <el-tooltip
            :content="row.status"
            class="item"
            effect="dark"
            placement="top-start"
          >
            <el-tag :type="row.status | statusFilter">
              {{ row.status }}
            </el-tag>
          </el-tooltip>
        </template>
      </u-table-column>
      <u-table-column
        show-overflow-tooltip
        label="时间"
        prop="datetime"
        width="200"
      ></u-table-column>
      <u-table-column show-overflow-tooltip label="操作" width="180px">
        <template #default="{ row }">
          <el-button type="text" @click="handleEdit(row)">编辑</el-button>
          <el-button type="text" @click="handleDelete(row)">删除</el-button>
        </template>
      </u-table-column>
    </u-table>
    <!-- <el-pagination
      :background="background"
      :current-page="queryForm.pageNo"
      :layout="layout"
      :page-size="queryForm.pageSize"
      :total="total"
      @current-change="handleCurrentChange"
      @size-change="handleSizeChange"
    ></el-pagination> -->
    <table-edit ref="edit"></table-edit>
  </div>
</template>

<script>
  import { getList, doDelete } from '@/api/table'
  import TableEdit from './components/TableEdit'
  export default {
    name: 'ComprehensiveTable',
    components: {
      TableEdit,
    },
    filters: {
      statusFilter(status) {
        const statusMap = {
          published: 'success',
          draft: 'gray',
          deleted: 'danger',
        }
        return statusMap[status]
      },
    },
    data() {
      return {
        imgShow: true,
        list: [],
        imageList: [],
        listLoading: true,
        layout: 'total, sizes, prev, pager, next, jumper',
        total: 0,
        background: true,
        selectRows: '',
        elementLoadingText: '正在加载...',
        queryForm: {
          pageNo: 1,
          pageSize: 500,
          title: '',
        },
        pageSizes:[10, 20, 50, 100, 500, 1000]
      }
    },
    computed: {
      height() {
        return this.$baseTableHeight()
      },
    },
    created() {
      this.fetchData()
    },
    beforeDestroy() {},
    mounted() {},
    methods: {


      selectable (row, index) {
          if (index === 1) {
              return false
          } else {
              return true
          }
      },

      tableSortChange() {
        const imageList = []
        this.$refs.tableSort.data.forEach((item, index) => {
          imageList.push(item.img)
        })
        this.imageList = imageList
      },
      handlePageSize({page, size}){
        console.log(page, size)
        this.queryForm.pageSize = size
        this.queryForm.pageNo = page
        this.fetchData()
      },
      setSelectRows(val) {
        console.log(val);
        // this.selectRows = val
      },
      tableSelectChange(tableSelectData){
        console.log(tableSelectData);

      },
      handleAdd() {
        this.$refs['edit'].showEdit()
      },
      handleEdit(row) {
        this.$refs['edit'].showEdit(row)
      },
      handleDelete(row) {
        if (row.id) {
          this.$baseConfirm('你确定要删除当前项吗', null, async () => {
            const { msg } = await doDelete({ ids: row.id })
            this.$baseMessage(msg, 'success')
            this.fetchData()
          })
        } else {
          if (this.selectRows.length > 0) {
            const ids = this.selectRows.map((item) => item.id).join()
            this.$baseConfirm('你确定要删除选中项吗', null, async () => {
              const { msg } = await doDelete({ ids: ids })
              this.$baseMessage(msg, 'success')
              this.fetchData()
            })
          } else {
            this.$baseMessage('未选中任何行', 'error')
            return false
          }
        }
      },
      // handleSizeChange(val) {
      //   this.queryForm.pageSize = val
      //   this.fetchData()
      // },
      // handleCurrentChange(val) {
      //   this.queryForm.pageNo = val
      //   this.fetchData()
      // },
      handleQuery() {
        this.queryForm.pageNo = 1
        this.fetchData()
      },
      async fetchData() {
        this.listLoading = true
        const { data, totalCount } = await getList(this.queryForm)
        this.list = data;
        const imageList = []
        data.forEach((item, index) => {
          imageList.push(item.img)
        })
        this.imageList = imageList
        this.total = totalCount
        setTimeout(() => {
          this.listLoading = false
        }, 500)
      },
      testMessage() {
        this.$baseMessage('test1', 'success')
      },
      testALert() {
        this.$baseAlert('11')
        this.$baseAlert('11', '自定义标题', () => {
          /* 可以写回调; */
        })
        this.$baseAlert('11', null, () => {
          /* 可以写回调; */
        })
      },
      testConfirm() {
        this.$baseConfirm(
          '你确定要执行该操作?',
          null,
          () => {
            /* 可以写回调; */
          },
          () => {
            /* 可以写回调; */
          }
        )
      },
      testNotify() {
        this.$baseNotify('测试消息提示', 'test', 'success', 'bottom-right')
      },
    },
  }
</script>
<style lang="scss" scoped>
  .el-table--small .el-table__cell{
      padding: $base-table-small-padding;
  }
</style>

