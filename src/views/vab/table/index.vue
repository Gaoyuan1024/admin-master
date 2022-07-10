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

    <table-list 
      ref="multipleTable" 
      :data="list" 
      :columns="columns"
      :settings="tableSettings"
      :queryForm="queryForm"
      @selection="setSelectRows"
      @tableSelectChange="tableSelectChange"
      @pageSize="handleSizeChange"
      @currentPage="handleCurrentChange"
      >
      <!-- <template slot="avatar">
        <u-table-column show-overflow-tooltip label="头像">
          <template #default="{ row }">
            <el-image
              :preview-src-list="imageList"
              :src="row.img"
            ></el-image>
          </template>
        </u-table-column>
      </template> -->

      <template slot="action">
        <u-table-column show-overflow-tooltip label="操作" width="180px">
          <template #default="{ row }">
            <el-button type="text" @click="handleEdit(row)">编辑</el-button>
            <el-button type="text" @click="handleDelete(row)">删除</el-button>
          </template>
        </u-table-column>
      </template>
    </table-list>

    <table-edit ref="edit"></table-edit>
  </div>
</template>

<script>
  import { getList, doDelete } from '@/api/table'
  import TableEdit from './components/TableEdit'
  import TableList from '@/components/TableList'
  export default {
    name: 'ComprehensiveTable',
    components: {
      TableEdit,
      TableList,
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
        columns: [
          { label: '标题',   prop: 'title',     align: 'center' },
          { label: '作者',   prop: 'author',    align: 'center' },
          // { label: '头像',   prop: 'avatar',    align: 'center', __slotName: 'avatar' },
          { label: '点击量', prop: 'pageViews', align: 'center' , sortable: true},
          { label: '时间',   prop: 'datetime',  align: 'center' },
        ],       
        list: [],
        imageList: [],
        selectRows: [],
        queryForm: {
          pageNo: 1,
          pageSize: 500,
          title: '',
        },
        tableSettings: {
          isSelection:true,
          isPagination: true,
          listLoading: true,
          total: 0,
        },     
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
        this.$refs.multipleTable.data.forEach((item, index) => {
          imageList.push(item.img)
        })
        this.imageList = imageList
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
      handleSizeChange(val) {
        this.queryForm.pageSize = val
        this.fetchData()
      },
      handleCurrentChange(val) {
        this.queryForm.pageNo = val
        this.fetchData()
      },
      handleQuery() {
        console.log(this.$refs);
        this.$refs.multipleTable.$children[0].clearSelection()
        this.queryForm.pageNo = 1;
        this.fetchData()
      },
      async fetchData() {
        this.tableSettings.listLoading = true;
        const { data, totalCount } = await getList(this.queryForm)
        this.list = data;
        const imageList = []
        data.forEach((item, index) => {
          imageList.push(item.img)
        })
        this.imageList = imageList
        this.tableSettings.total = totalCount
        setTimeout(() => {
          this.tableSettings.listLoading = false
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

