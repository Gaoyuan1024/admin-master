<template>
  <div class="table-container">

    <flight-form
      ref="searchForm"
      :searchForm="searchForm"
      :searchData="searchData"
      :searchChildForm="searchChildForm"
      :searchChildData="searchChildData"
      :showSearch="showSearch"
      @sendSearch="sendSearch"
      @sendReset="resetForm"
      @update:changeHeight="changeHeight"
    ></flight-form>

    <!-- <vab-query-form>
      <vab-query-form-left-panel>
        <el-button type="primary" @click="handleAdd">
          添加
        </el-button>
        <el-button type="danger" @click="handleDelete">
          删除
        </el-button>

        <el-button type="primary" @click="handleSelect">
          选择
        </el-button>
        <el-button type="primary" @click="testMessage">baseMessage</el-button>
        <el-button type="primary" @click="testALert">baseAlert</el-button>
        <el-button type="primary" @click="testConfirm">baseConfirm</el-button>
        <el-button type="primary" @click="testNotify">baseNotify</el-button>
      </vab-query-form-left-panel>
      <vab-query-form-right-panel>
        <el-button type="primary" @click="handleSelect">
          选择
        </el-button>

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
    </vab-query-form> -->

    <table-list 
      ref="multipleTable" 
      :data="list"
      :TopAction="TopAction" 
      :columns="columns"
      :settings="tableSettings"
      :queryForm="queryForm"
      @handleTopAction="handleTopAction"
      @selection="setSelectRows"
      @tableSelectChange="tableSelectChange"
      @pageSize="handleSizeChange"
      @currentPage="handleCurrentChange"
      >
      <template slot="avatar">
        <u-table-column show-overflow-tooltip label="头像">
          <template #default="{ row }">
            <el-image
              :preview-src-list="imageList"
              :src="row.img"
            ></el-image>
          </template>
        </u-table-column>
      </template>
      <!-- <template slot="status">
        <u-table-column show-overflow-tooltip label="状态">
          <template #default="{ row }">
              <el-tag :type="row.status | statusFilter">
                {{ row.status }}
              </el-tag>
          </template>
        </u-table-column>
      </template> -->
      <u-table-column 
        show-overflow-tooltip 
        slot="action" 
        fixed="right" 
        label="操作" 
        width="180px">
        <template #default="{ row }">
          <el-button size="mini" type="text" @click="handleDetail(row)">详情</el-button>
          <el-button size="mini" type="text" @click="handleEdit(row)">编辑</el-button>
          <el-button size="mini" type="text" @click="handleDelete(row)">删除</el-button>
        </template>
      </u-table-column>
    </table-list>

    <table-edit ref="edit"></table-edit>
    
    <table-select ref="select"></table-select>
  </div>
</template>

<script>
  import { getList, doDelete } from '@/api/table'
  import TableList from './components/TableList'
  import TableEdit from './components/TableEdit'
  import TableSelect from './components/TableSelect'
  import FlightForm from './components/FlightForm'
import tree from '../../../../mock/controller/tree'

  export default {
    name: 'ComprehensiveTable',
    components: {
      TableList,
      TableEdit,
      FlightForm,
      TableSelect
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
        showSearch: {
          reset: true,
          more: true
        },
        searchChildForm:[],
        searchChildData:{},
        searchForm:[
          {
            type: "input",
            label: "输入框",
            prop: "input",
            show: true,
          },
          {
            type: "select",
            label: "下拉框",
            prop: "select",
            show: true,
            options: [{
              value: '选项1',
              label: '黄金糕'
            }, {
              value: '选项2',
              label: '双皮奶'
            }, {
              value: '选项3',
              label: '蚵仔煎'
            }, {
              value: '选项4',
              label: '龙须面'
            }, {
              value: '选项5',
              label: '北京烤鸭'
            }],
          },
          // {
          //   type: "cascader",
          //   label: "级联选择器",
          //   prop: "cascader",
          //   show: true,
          //   options: [{
          //     value: '选项1',
          //     label: '黄金糕'
          //   }, {
          //     value: '选项2',
          //     label: '双皮奶'
          //   }, {
          //     value: '选项3',
          //     label: '蚵仔煎'
          //   }, {
          //     value: '选项4',
          //     label: '龙须面'
          //   }, {
          //     value: '选项5',
          //     label: '北京烤鸭'
          //   }],

          // },
          // {
          //   type: "Radio",
          //   label: "单选",
          //   prop: "Radio",
          //   radios:[{
          //     value: '选项1',
          //     label: '黄金糕'
          //   }, {
          //     value: '选项2',
          //     label: '双皮奶'
          //   }, {
          //     value: '选项3',
          //     label: '蚵仔煎'
          //   }],
          // },
          // {
          //   type: "RadioButton",
          //   label: "单选按钮",
          //   prop: "RadioButton",
          //   radios:[{
          //     value: '选项1',
          //     label: '黄金糕'
          //   }, {
          //     value: '选项2',
          //     label: '双皮奶'
          //   }, {
          //     value: '选项3',
          //     label: '蚵仔煎'
          //   }],
          // },
          // {
          //   type: "Checkbox",
          //   label: "Checkbox",
          //   prop: "Checkbox",
          //   checkboxs:[{
          //     value: '选项1',
          //     label: '黄金糕'
          //   }, {
          //     value: '选项2',
          //     label: '双皮奶'
          //   }, {
          //     value: '选项3',
          //     label: '蚵仔煎'
          //   }],
          // },
          {
            type: "inputNumber",
            label: "计数器",
            prop: "inputNumber",
            show: true,
          },
          {
            type: "Time",
            label: "时间",
            prop: "Time",
            show: false,
          },
          {
            type: "Date",
            label: "日期",
            prop: "Date",
            show: false,
          },
          {
            type: "DateTime",
            label: "日期时间",
            prop: "DateTime",
            show: false,
          },
        ],
        searchData:{
          input:'',
          select:'',
          cascader:'',
          Radio:'',
          RadioButton:'',
          Checkbox:'',
          inputNumber:'',
          Time:'',
          Date:'',
          DateTime:'',
        },
        TopAction:{
          isTopAction: true,
          left:[
            {label: '添加',},
            {label: '删除',},
            {label: '选择',},
          ],
          right:[
            {label: '设置拖拽列',   type:'primary', dropdown: true},
          ],
        },
        columns: [
          { selection: true, fixed: true, },
          { index: true,  },
          { label: '标题',   prop: 'title',      },
          { label: '作者',   prop: 'author',     },
          // { slot: 'avatar',  label: '头像',   prop: 'avatar',     },
          { label: '点击量', prop: 'pageViews', sortable: true, },
          // { label: '状态',   prop: 'status',   slot: 'status',  },
          // { label: '状态',   prop: 'status',   slot: 'status',  },
          {
            label: "状态",
            prop: "status",
            render: (h, params) => {
              return h("div", [
                h("el-tag", {
                  domProps:{
                    innerHTML: params.row.status,
                  },
                  props: {
                    type: this.$options.filters.statusFilter(params.row.status)
                  },
                  // on: {
                  //   click: () => {
                  //     alert("我是查看页面");
                  //   },
                  // },
                }),
              ]);
            },
          },
          { label: '时间',   prop: 'datetime', },
          { slot: 'action', },
          // {
          //   width: "300",
          //   prop: "action",
          //   label: "操作",
          //   render: (h, params) => {
          //     return h("div", [
          //       h("a", {
          //         domProps: {
          //           innerHTML: "查看",
          //         },
          //         style: {
          //           marginRight: "10px",
          //         },
          //         on: {
          //           click: () => {
          //             alert("我是查看页面");
          //           },
          //         },
          //       }),
          //     ]);
          //   },
          // },

        ],       
        list: [],
        imageList: [],
        selectRows: [],
        queryForm: {
          pageNo: 1,
          pageSize: 10,
          title: '',
        },
        tableSettings: {
          isSelection:true,
          isIndex:true,
          isPagination: true,
          listLoading: true,
          total: 0,
        },     
      }
    },
    computed: {
      height() {
        // let top = this.TopAction.isTopAction?
        // 42:0;
        // console.log(this.$baseTableHeight());
        return this.$baseTableHeight() - 100;
      },
    },
    created() {
      this.fetchData()
    },
    beforeDestroy() {},
    mounted() {},
    methods: {
      changeHeight(value){
        console.log(value);
        if(value){
          this.searchForm.forEach((element,index) => {
            index === 0||index === 1||index === 2?
            element.show = true:element.show = false
          });

        }else{
          this.searchForm.forEach(element => {
            element.show = true
          });
        }
      },
      sendSearch(searchData, moreData) {
        console.log('111');
        this.searchData = Object.assign(searchData, moreData);
        // this.submitForm(1);  // 查询表格的方法
        this.handleQuery()
      },
      resetForm() {
        console.log('222');
        Object.assign(this.$data.searchData, this.$options.data.call(this).searchData);
        Object.assign(this.$data.searchChildData, this.$options.data.call(this).searchChildData);
      },
      selectable (row, index) {
          if (index === 1) {
              return false
          } else {
              return true
          }
      },
      setSelectRows(val) {
        console.log(val);
        this.selectRows = val
      },
      tableSelectChange(tableSelectData){
        console.log(tableSelectData);

      },
      handleTopAction(type){
        const actionBtn = {
          '添加': this.handleAdd,
          '删除': this.handleDelete,
          '选择': this.handleSelect,
          '设置拖拽列': '',
        };
        if (type in actionBtn) {
          actionBtn[type]();
        }
      },

      handleAdd(type) {

        this.$refs['edit'].showEdit()
      },
      handleSelect(){
        this.$refs['select'].showSelect(this.selectRows)
      },
      handleDetail(row) {
        // this.$refs['edit'].showEdit(row)
        this.$router.push({
          path:'/vab/table/detail',
          query:{
            ...row
          }
        });
      },
      handleEdit(row) {
        this.$refs['edit'].showEdit(row)
      },
      handleDelete(row) {
        if (row&&row.id) {
          this.$baseConfirm('你确定要删除当前项吗', null, async () => {
            const { msg } = await doDelete({ ids: row.id })
            this.$baseMessage(msg, 'success')
            this.fetchData()
          })
        } else {
          console.log('111');
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
        this.$refs.multipleTable.$children[1].clearSelection()
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
    watch: {

    }

  }
</script>
<style lang="scss" scoped>
  .el-table--small .el-table__cell{
      padding: $base-table-small-padding;
  }
</style>

