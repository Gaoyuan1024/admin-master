<template>
  <el-dialog v-dialogDrag
    :title="title"
    :visible.sync="dialogFormVisible"
    width="900px"
    @close="close"
  >

    <table-list 
        ref="selectTable" 
        :data="list" 
        :columns="columns"
        :settings="tableSettings"
        :queryForm="queryForm"

        :searchForm="searchForm"
        :searchData="searchData"
        :searchChildForm="searchChildForm"
        :searchChildData="searchChildData"
        :showSearch="showSearch"
        @sendSearch="sendSearch"
        @sendReset="resetForm"
        @update:changeHeight="changeHeight"

        @selection="setSelectRows"
        @tableSelectChange="tableSelectChange"
        @pageSize="handleSizeChange"
        @currentPage="handleCurrentChange">
    </table-list>
    <div slot="footer" class="dialog-footer">
      <el-button @click="close">取 消</el-button>
      <el-button type="primary" @click="save">确 定</el-button>
    </div>
  </el-dialog>
</template>

<script>
  import { doEdit, getList, } from '@/api/table'
  import TableList from './TableList'

  export default {
    name: 'TableSelect',
    components: {
      TableList,
    },
    data() {
      return {
        title: '',
        dialogFormVisible: false,

        showSearch: {
          reset: true,
          more: false
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
        
        columns: [
          { label: '',selection: true, fixed: true, },
          { label: '序号',index: true,  },
          { label: '标题',   prop: 'title',      },
          { label: '作者',   prop: 'author',     },
          // { slot: 'avatar',  label: '头像',   prop: 'avatar',     },
          { label: '点击量', prop: 'pageViews', sortable: true, },
          { label: '状态',   prop: 'status',   slot: 'status',  },
          { label: '时间',   prop: 'datetime',   },
          { label: '操作',   slot: 'action', },

        ],       
        list: [],
        checkarr: [],
        queryForm: {
          pageNo: 1,
          pageSize: 10,
          title: '',
        },
        tableSettings: {
          height:500,
          isSelection:true,
          isIndex:true,
          isPagination: true,
          listLoading: true,
          total: 0,
        }, 
      }
    },
    created() {},
    methods: {

      changeHeight(value){
        console.log(value);
        if(value){
          this.searchForm.forEach((element,index) => {
            index === 0||index === 1?
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

      showSelect(row) {
        if (!row) {
          this.title = '添加'
        } else {
          this.title = '选择器'
          this.checkarr = [...row]
          console.log(this.checkarr);
        //   this.form = Object.assign({}, row)
          this.fetchData()
          this.$nextTick(()=>{
            console.log(this.$refs);
              this.$refs.selectTable.$children[1].doLayout();
          })

        }
        this.dialogFormVisible = true
      },

      // 适用于多量的数据选中
      partRowSelections (state) {
          // 获取前面的500条数据。实际场景自己去给你要选中的数据
          let data = this.list.slice(0,10)
          console.log(this.$refs);
          // data是数据，state是选中还是取消选中
          this.$refs.selectTable.$children[1].partRowSelections(data, state)
      },

      setSelectRows(val) {
        console.log(val);
        // this.selectRows = val
      },
      tableSelectChange(tableSelectData){
        console.log(tableSelectData);

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
        this.$refs.selectTable.$children[1].clearSelection()
        this.queryForm.pageNo = 1;
        this.fetchData()
      },
      async fetchData() {
        this.tableSettings.listLoading = true;
        const { data, totalCount } = await getList(this.queryForm)
        this.list = data;
        data.forEach((item, index) => {
          this.checkarr.forEach(element => {
            if (item.id === element.id) {
            // 等表格数据加载完成后
              this.$nextTick(() => {
                console.log(this.$refs);
                // data是数据，state是选中还是取消选中
                this.$refs.selectTable.$children[1].partRowSelections(item, true)
              })
            }
          });

        })  
        this.tableSettings.total = totalCount
        setTimeout(() => {
          this.tableSettings.listLoading = false
        }, 500)
      },

      close() {
        this.$refs.selectTable.$children[1].clearSelection()
        this.queryForm = this.$options.data().queryForm
        this.dialogFormVisible = false
        // this.$emit('fetch-data')
      },
      save() {
        this.$refs['form'].validate(async (valid) => {
          if (valid) {
            const { msg } = await doEdit(this.form)
            this.$baseMessage(msg, 'success')
            this.$refs['form'].resetFields()
            this.dialogFormVisible = false
            this.$emit('fetch-data')
            this.form = this.$options.data().form
          } else {
            return false
          }
        })
      },
    },
  }
</script>
<style lang="scss" scoped>
::v-deep {
  .el-dialog__body {
    padding: 12px 12px;
  }

}
</style>