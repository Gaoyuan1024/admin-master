<template>
  <div>
    <u-table
      v-loading="settings.listLoading"
      :data="data"
      :size="'small'"     
      :border="true"
      stripe
      :height="height"
      :row-height="50"
      :element-loading-text="'正在加载...'"
      row-key="id"
      record-table-select
      use-virtual
      big-data-checkbox
      highlight-current-row
      @selection-change="(e) => handleClick('selection', e)"
      @table-select-change="tableSelectChange"
      >
      <u-table-column
        v-if="settings.isSelection"
        show-overflow-tooltip
        type="selection"
        width="55"
        fixed
        reserve-selection
      ></u-table-column>
      <u-table-column 
        v-if="settings.isIndex"
        fixed
        show-overflow-tooltip 
        label="序号" 
        width="95">
        <template #default="scope">
          {{ scope.$index + 1 }}
        </template>
      </u-table-column>

      <template v-for="(item) in columns">
        <template v-if="item.prop !== 'action' && !item.__slotName">
        <u-table-column
            :prop="item.prop"
            :label="item.label"
            :sortable="item.sortable"
            show-overflow-tooltip>
            <template v-if="item.__slotName" v-slot="scope">
                <slot :name="item.__slotName" :data="scope"></slot>
            </template>      
        </u-table-column>        
        </template> 

        <template v-if="item.__slotName">
            <slot :name="item.prop"></slot>
        </template>

      </template>

      <slot name="action"></slot>
    </u-table>

    <el-pagination
        v-if="settings.isPagination"
        background
        @size-change="(e) => handleClick('pageSize', e)"
        @current-change="(e) => handleClick('currentPage', e)"
        :current-page="queryForm.pageNo"
        :page-sizes="[10, 20, 50, 100, 500, 1000]"
        :page-size="queryForm.pageSize"
        layout="total, sizes, prev, pager, next, jumper"
        :total="settings.total"
    ></el-pagination>
  </div>
</template>

<script>
  import { getList, doDelete } from '@/api/table'
  export default {
    name: 'TableList',
    props: {
        data: {
            type: Array,
            default: () => [],
        },
        columns: {
            type: Array,
            default: () => [],
        },
        settings: {
            type: Object,
            default: () => {
                return {
                    listLoading: false,   //加载数据时显示动效
                    isIndex: false,     //是否需要序列号,默认不需要
                    isSelection: false, //是否有多选
                    isPagination: false,//是否添加分页,默认false,
                    total: 0,          //总条数
                };
            },
        },
        queryForm: {
            type: Object,
            default: () => {
                return {
                    pageNo: 1,
                    pageSize: 500,
                };
            },
        },
    },    
    components: {
    },
    computed: {
      height() {
        return this.$baseTableHeight()
      },
    },
    methods: {
      handleClick(type, e) {
        switch (type) {
          case "selection":
            this.$emit("selection", e);
            break;

          case "pageSize":   //一页多少条
            this.$emit("pageSize", e);
            break;
        
          case "currentPage"://第几页
            this.$emit("currentPage", e);
            break;
                
          default:
            break;
        }
      },
      tableSelectChange(val){
        this.$emit("tableSelectChange", val);
      },
    },
  }
</script>
<style lang="scss" scoped>
</style>