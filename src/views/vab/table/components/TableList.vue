<template>
  <div>

    <el-form ref="form" 
      :inline="true" 
      label-position="right" 
      label-width="80px" 
      class="demo-form-inline">
        <!-- 前部插槽 -->
        <slot name="front"></slot>
        <!-- span默认值为4  -->
        <!-- 依据span值可计算出xs sm md lg xl的默认值 也可自行设置 -->
        <!-- pull 向左移动格数 push 向右移动格数 offset 和左侧的间隔格数 -->
          <el-form-item
            v-for="(item, index) in searchForm"
            :key="item.prop"
            :label="item.label ? item.label : ' '"
            v-show="item.show === false ? item.show : true"
            :class="item.className"
          >
            <!-- 输入框 -->
            <el-input
              v-if="item.type == 'input'"
              v-model="searchData[item.prop]"
              placeholder="选择输入"
            ></el-input>

            <!-- 下拉框 -->
            <el-select
              v-if="item.type == 'select'"
              v-model="searchData[item.prop]"
              :multiple="item.multiple"
              :collapse-tags="item.collapseTags"
              :filterable="item.filterable"
              :remote="item.remote" 
              :remote-method="
                (query) => {
                  remoteMethod(query, item.options)
                }
              "
              :loading="remoteLoading"
            >
              <!-- 远程搜索 -->
              <el-option
                v-for="ele in item.options"
                :key="ele.value"
                :label="ele.label"
                :value="ele.value"
              >
              </el-option>
            </el-select>
            <!-- 级联选择器 -->
            <el-cascader
              v-if="item.type == 'cascader'"
              v-model="searchData[item.prop]"
              :options="item.options"
              :placeholder="item.placeholder ? item.placeholder : '请选择'"
            ></el-cascader>

            <!-- 单选 -->
            <el-radio-group
              v-if="item.type === 'Radio'"
              v-model="searchData[item.prop]"
            >
              <el-radio
                v-for="ra in item.radios"
                :label="ra.value"
                :key="ra.value"
              >{{ ra.label }}
              </el-radio
              >
            </el-radio-group>

            <!-- 单选按钮 -->
            <el-radio-group
              v-if="item.type === 'RadioButton'"
              v-model="searchData[item.prop]"
              @change="item.change && item.change(searchData[item.prop])"
            >
              <el-radio-button
                v-for="ra in item.radios"
                :label="ra.value"
                :key="ra.value"
              >{{ ra.label }}
              </el-radio-button
              >
            </el-radio-group>

            <!-- 复选框 -->
            <el-checkbox-group
              v-if="item.type === 'Checkbox'"
              v-model="searchData[item.prop]"
            >
              <el-checkbox
                v-for="ch in item.checkboxs"
                :label="ch.value"
                :key="ch.value"
              >{{ ch.label }}
              </el-checkbox
              >
            </el-checkbox-group>

            <!-- 计数器 -->
            <el-input-number
              v-if="item.type === 'inputNumber'"
              controls-position="right"
              :controls="false"
              v-model="searchData[item.prop]"
              placeholder="选择输入"
              :min="item.minNum"
              :max="item.maxNum"
            ></el-input-number>

            <!-- 时间 -->
            <el-time-select
              v-if="item.type === 'Time'"
              v-model="searchData[item.prop]"
              placeholder="选择时间"
              @change="DateTimeChang"
            ></el-time-select>

            <!-- 日期 -->
            <el-date-picker
              v-if="item.type === 'Date'"
              v-model="searchData[item.prop]"
              placeholder="选择时间"
              @change="DateTimeChang"
            ></el-date-picker>
            

            <!-- 日期时间 -->
            <el-date-picker
              v-if="item.type === 'DateTime'"
              type="datetime"
              :picker-options="
                item.pickerOptions
                  ? item.pickerOptions
                  : setOptions(item, index)
              "
              v-model="searchData[item.prop]"
              placeholder="选择时间"
              :format="
                item.dateFormat ? item.dateFormat : 'yyyy-MM-dd HH:mm:ss'
              "
              :value-format="
                item.dateFormat ? item.dateFormat : 'yyyy-MM-dd HH:mm:ss'
              "
              :disabled="item.disable && item.disable(searchData[item.prop])"
            >
            </el-date-picker>
          </el-form-item>
        <!-- 尾部插槽 -->
        <slot name="last"></slot>
        <!-- 查询按钮 -->
          <el-button
            type="primary"
            @click="sendSearch"
            class="scpp-button"
          >搜索
          </el-button>
          <el-button
            class="scpp-reset-button"
            @click="reset"
            v-if="showSearch.reset"
          >重置
          </el-button>
          <span
            @click="more"
            style="color: #1796ff;cursor: pointer;margin: 0 0 0 10px!important;"
            v-if="showSearch.more"
          >{{showCondition?'展开':'合并'}}
            <i
              :class="[
                showCondition
                  ? 'el-icon-arrow-down'
                  : 'el-icon-arrow-up',
                'arrow-icon',
              ]"
            /></span
          >
    </el-form>

    <vab-query-form v-if="TopAction && TopAction.isTopAction">
      <vab-query-form-left-panel>
        <template v-for="(item, index) in TopAction.left">
          <el-button :type="item.type?item.type:'primary'" @click="(e) => handleTopAction(item.label, e)">
            {{item.label}}
          </el-button>  
        </template>
      </vab-query-form-left-panel>
      <vab-query-form-right-panel>
        <template v-for="(item, index) in TopAction.right">
          <el-dropdown v-if="item.dropdown" 
            trigger="click" 
            placement="bottom">
            <el-button :type='item.type?item.type:"primary"'>
               {{item.label}}<i class="el-icon-arrow-down el-icon--right"></i>
            </el-button>
            <el-dropdown-menu slot="dropdown">
              <el-tree
                ref="draggable_tree"
                :data="dragHeaders"
                :props="defaultProps"
                node-key="label"
                :default-checked-keys="defaultChecked"
                default-expand-all
                show-checkbox
                draggable
                :filter-node-method="filterNodeTree"
                :allow-drop="allowDrop"
                :expand-on-click-node="false"
                @check="check"
                @node-drop="sort"
                @check-change="handleCheckChange">  
                <span class="custom-tree-node" slot-scope="{ node, data }">
                  <span>{{ node.label }}{{ node.fixed }}</span>
                  <span> 
                    <i 
                      :class="[
                        data.fixed === 'left'
                          ? 'arrow-icon'
                          : '',
                        'el-icon-caret-left',
                      ]"                    
                      @click="() => leftFlex(node, data)"
                      ></i>
                    <i 
                      :class="[
                        data.fixed === 'right'
                          ? 'arrow-icon'
                          : '',
                        'el-icon-caret-right',
                      ]" 
                      @click="() => rightFlex(node, data)"
                      ></i>
                  </span>
                </span>                                
              </el-tree>
            </el-dropdown-menu>
          </el-dropdown>
          <el-button v-else :type='item.type?item.type:"primary"' @click="(e) => handleTopAction(item.label, e)">
            {{item.label}}
          </el-button> 

        </template>
      </vab-query-form-right-panel>
    </vab-query-form>

    <u-table
      v-loading="settings.listLoading"
      style="width: 100%"
      :data="data"
      :size="'mini'"     
      :border="true"
      ref="table"
      stripe
      :height="settings.height|| height"
      :row-height="40"
      :element-loading-text="'正在加载...'"
      row-key="id"
      record-table-select
      use-virtual
      big-data-checkbox
      fixed-columns-roll
      highlight-current-row
      @selection-change="(e) => handleClick('selection', e)"
      @table-select-change="tableSelectChange"
      >

      <template v-for="(item, index) in dragHeaders">
        <!-- 选择框 -->
        <u-table-column
          v-if="item.selection && item.isShow"
          type="selection"
          width="55"
          :fixed="item.fixed"
          show-overflow-tooltip
          reserve-selection
        ></u-table-column>   
        <!-- 序号 -->   
        <u-table-column 
          v-else-if="item.index && item.isShow"
          type="index"
          width="95"
          fixed
          show-overflow-tooltip 
          label="序号" >
          <template #default="scope">
            {{ scope.$index + 1 }}
          </template>          
        </u-table-column>
        <u-table-column 
          v-else-if="item.index && item.isShow"
          type="index"
          width="95"
          fixed
          show-overflow-tooltip 
          label="序号" >
          <template #default="scope">
            {{ scope.$index + 1 }}
          </template>          
        </u-table-column>
        <!-- 自定义内容 -->
        <slot
          v-else-if="item.slot && item.isShow"
          show-overflow-tooltip
          :fixed="item.fixed"
          :name="item.slot"></slot>
        <!-- 常规字段 -->
        <u-table-column
          v-else-if="item.render && item.isShow"
          show-overflow-tooltip
          v-bind="item"
          :fixed="item.fixed"          
        >
        <template v-slot="scope">
            <ex-slot v-if="item.render" :render="item.render" :row="scope.row" :index="scope.$index" :column="item" />
            <span v-else>{{ scope.row[item.key]  }}</span>
        </template>
        </u-table-column>          
        <!-- 常规字段 -->
        <u-table-column
          v-else-if="item.isShow"
          show-overflow-tooltip
          v-bind="item"
          :fixed="item.fixed"          
        >
        </u-table-column>          
      </template>

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
  import { getList, doDelete } from '@/api/table';

  // 自定义内容的组件
  var exSlot = {
    functional: true,
    props: {
      row: Object,
      render: Function,
      index: Number,
      column: {
        type: Object,
        default: null
      }
    },
    render: (h, data) => {
      const params = {
        row: data.props.row,
        index: data.props.index
      }
      if (data.props.column) params.column = data.props.column
      return data.props.render(h, params)
    },
  }

  export default {
    
    name: 'TableList',
    props: {
      searchForm: {
        type: Array,
      },
      searchData: {
        type: Object,
      },
      searchChildForm: {
        type: Array,
      },
      searchChildData: {
        type: Object,
      },
      showSearch: {
        type: Object,
      },
      changeHeight: {
        type: Boolean,
        default: false,
      },
    
      TopAction: {
          type: Object,
          default: () => {},
      },
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
      exSlot
    },
    data() {
      return {
        remoteOptions: [],
        remoteLoading: false,
        showCondition: true,   

        headers: [],
        dragHeaders:[],

        // treeData: [{
        //   label: '一级 1', id: 111,
        // }, {
        //   label: '一级 2', id: 222,
        // }, {
        //   label: '一级 3', id: 333,
        // }],
        defaultChecked: [],
        defaultProps: {
          label: 'label',
          children: 'children',
          
        }

      }
    },
    created () {
      this.headers = this.columns.map((c) => {
        if (c.isShow === undefined) {
          this.$set(c, 'isShow', true);
          this.$set(c, 'fixed', '');
          this.defaultChecked.push(c.label)
        }
        return c
      })
      console.log(this.headers);
      
      this.dragHeaders = this.headers;
      this.filterNodeTree();
    },
    mounted (){
      this.$refs.draggable_tree?
      this.$refs.draggable_tree[0].filter():'';
    },
    computed: {
      height() {
        let top  = this.TopAction && this.TopAction.isTopAction?45:0,      
            more = this.showCondition?0:51;        
        return this.$baseTableHeight() - top - more;
      },
    },
    watch: {
      addressId(newVal) {
        if (newVal) {
          this.getArea()
        }
      },
      showCondition(newVal) {
        // console.log(newVal)
        //修改显示高度
        this.$emit("update:changeHeight", newVal);
      }
    },

    methods: {
      leftFlex(node, data){
        console.log(node, data);
        this.dragHeaders.map((c) => {
          if (c.label === data.label) {
            this.$set(c, 'fixed', data.fixed==='left'?'':'left');
          }
          return c;
        });
      },
      rightFlex(node, data){
        console.log(node, data);
        this.dragHeaders.map((c) => {
          if (c.label === data.label) {
            this.$set(c, 'fixed', data.fixed==='right'?'':'right');
          }
          return c;
        });
      },
      // 调用tree过滤方法 中文英过滤
      filterNodeTree (value, data, node) {        
        if (data && data.label) return true;
      },
      //只有点击的时候才会触发
      check(data, checkNodes) {
        console.log(this.$refs.table);
        //点击的时候获取选中的节点，更改defaultSelect
        this.defaultChecked = checkNodes.checkedKeys //选中的节点
        this.$nextTick(()=>{
            this.$refs.table.doLayout();
        })
      },

      //自定义选中方法
      setCheckedKeys() {
          console.log(this.$refs.draggable_tree);
          this.$refs.draggable_tree[0].setCheckedKeys(this.defaultChecked);
      },

      sort(draggingNode, dropNode, type, event) {
        console.log(draggingNode, dropNode, type, event)
        console.log(this.dragHeaders)
        
        // draggingNode.data.isShow
        this.setCheckedKeys();
        this.$nextTick(()=>{
            this.$refs.table.doLayout();
        })

      },
      allowDrop(draggingNode, dropNode, type) {
          // 同一父节点下实现拖拽
          if (draggingNode.data.label !== dropNode.data.label) {
            return type === 'prev' || type === 'next'
          } else {
            // 不同父节点处理
            return false
          }
      },

      handleCheckChange(data, checked, indeterminate) {
        console.log(checked);      
        // 设置表格列的显示与隐藏
        this.dragHeaders.map((c) => {
          if (c.label === data.label) {
            this.$set(c, 'isShow', checked)
          }
          return c
        });
      },
      

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
      handleTopAction(type) {
        this.$emit("handleTopAction", type);
      },
      tableSelectChange(val){
        this.$emit("tableSelectChange", val);
      },

      DateTimeChang(value,type){
        console.log(value,type);
        this.$emit('changeDateTimeValue',"改变它吧"); //通知父组件改变。
      },

      // 依据传入的值类型 判断是开始时间还是结束时间
      // 遵循结束时间大于开始时间的原则即可
      setOptions(item, index) {
        if (item.startTime) {
          let end = this.searchForm[index + 1]
          let disabledEnd = {
            disabledDate: (time) => {
              return (
                new Date(this.searchData[end.prop]).getTime() < time.getTime()
              )
            },
          }
          return disabledEnd
        } else if (item.endTime) {
          let start = this.searchForm[index - 1]
          let disabledStart = {
            disabledDate: (time) => {
              return (
                new Date(this.searchData[start.prop]).getTime() > time.getTime()
              )
            },
          }
          return disabledStart
        }
      },
      // 点击树时获取到当前的code以及name
      treeCheck(node) {
        let prop = ""
        let id = ""
        this.searchForm.forEach((item) => {
          if (item.type == "tree") {
            prop = item.prop
            id = item.propId
          }
        })
        this.$set(this.searchData, prop, node.shortName)
        this.$set(this.searchData, id, node.orgCode)
      },
      // 查询
      sendSearch() {
        this.showCondition = true
        this.$emit("sendSearch", this.searchData, this.searchChildData)
      },
      // 重置
      reset() {
        this.showCondition = true
        if (this.$refs.place) {
          this.$refs.place[0].clear()
        }
        this.$emit("sendReset")
      },
      more() {
        if (this.showCondition) {
          this.showCondition = false;
        } else {
          this.showCondition = true
        }


      },


    },
    
  }
</script>
<style lang="scss" scoped>
  .search-button {
    float: right;
  }
  .demo-form-inline .el-input {
    width: 180px!important;
  }

  .el-input-number {
    width: 100% !important;
    line-height: 30px !important;
  }

  /* .el-date-editor.el-input, .el-date-editor.el-input__inner {
    width: 100% !important;
  } */

  /* 更多图标 */
  .arrow-icon {
    color: #1796ff;
  }
  .custom-tree-node {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 14px;
    padding-right: 8px;
  }
  ::v-deep {
    .el-button--mini {
      padding: 6px 0px;
    }
    .el-tree{
      width: 140px;
    }
    .el-tree-node {
      padding: 3px 0;
    }
    .el-tree-node__content{
      padding: 0 15px!important;
    }
    .el-tree-node__expand-icon.is-leaf{
      display: none;
    }
    .right-panel > .el-form-item {
        margin: 5px;
    }
  }
</style>