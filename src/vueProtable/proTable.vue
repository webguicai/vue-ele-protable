<script>
import proPagination from "./proPagination.vue";
import proForm from "./proForm.vue";
import { omitBy } from "lodash";
export default {
  name: "vue-ele-protable",
  components: { proPagination, proForm },
  props: {
    tableColumns: { default: () => [], required: true }, // protable的表单列配置，同时生效于表单
    tableData: { default: () => [], required: true }, // 表格数据
    paginationProps: { default: () => ({}), required: true }, // 分页的其他属性都可以往里塞
    proFormData: { default: () => ({}), required: true }, // 绑定form数据
    protableRef: { default: "" }, // 绑定父级 data 中的 ref,注意传入string，是 data 中你第一的那个key的string类型
    toolbar: { default: () => [], required: false }, // 工具栏，你可以用来渲染按钮
    tableProps: { default: () => ({}), required: false }, // table的其他属性都可以往里塞
    formProps: { default: () => ({}), required: false }, // form的其他属性都可以往里塞
    otherRenders: { default: () => () => {}, required: false }, // 其他渲染，位于 toolbar 和 form 之间，可用来渲染title,tabs等
    customTable: { default: () => () => {}, required: false }, // 自定义 table
    loading: { default: false, required: false },
  },
  model: {
    prop: "proFormData",
    event: "formChange",
  },
  data() {
    return {
      searchRef: {},
      formInit: {},
      pagination: {
        pageSize: 20,
        currentPage: 1,
      },
    };
  },
  mounted() {
    const proFormDataInit = {};
    this.tableColumns.forEach((item) => {
      if (item.hideInSearch) return;
      proFormDataInit[item.prop] = undefined;
    });
    this.formInit = proFormDataInit;
    this.$emit("formChange", { ...proFormDataInit, ...this.proFormData });
    this.$parent[this?.protableRef] = this.$refs.elTable;
  },
  methods: {
    formChange(v) {
      this.$emit("formChange", v);
    },
    onSearch() {
      this.$emit(
        "onSearch",
        omitBy(
          { ...this.pagination, ...this.proFormData },
          (item) => item === undefined
        )
      );
    },
    onReset() {
      this.$emit("formChange", { ...this.formInit });
      this.pagination = { ...this.pagination, currentPage: 1 };
      setTimeout(() => {
        this.onSearch();
      });
    },
    renderFun(columns) {
      return columns.map((item) => {
        if (item.show === false || item.hideInTable) {
          return;
        } else {
          return (
            <el-table-column attrs={{ ...item }}>
              {item.children ? this.renderFun(item.children) : item.render}
            </el-table-column>
          );
        }
      });
    },
    customTableFun(Node) {
      return this.customTable()
        ? this.customTable(Node, this.tableProps, this.tableColumns)
        : Node;
    },
  },
  render() {
    const on = {
      ...this.$listeners,
    };
    return (
      <div style={{ width: "100%", padding: "20px" }}>
        {/* seach的Node */}
        <proForm
          searchItem={this.tableColumns}
          formProps={{
            onSearch: this.onSearch,
            onReset: this.onReset,
            ...this.formProps,
          }}
          proFormData={this.proFormData}
        />
        {/* otherRenders 的 Node */}
        <div class="proTableOtherRenders">{this.otherRenders()}</div>
        <div class="proTableToolbar">
          <div class="proTableToolbar-left">
            {this.toolbar?.left?.map((item) => item)}
          </div>
          <div class="proTableToolbar-right">
            {this.toolbar?.right?.map((item) => item)}
          </div>
        </div>
        {/* table 的 Node */}
        {this.customTableFun(
          <el-table
            data={this.tableData}
            attrs={{ style: "margin: 48px 0;", ...this.tableProps }}
            ref="elTable"
            v-loading={this.loading}
            {...{ on }}
          >
            {this.renderFun(this.tableColumns)}
          </el-table>
        )}
        {/* table 的 Node */}
        {this.tableData.length === 0 && !this.loading ? (
          <el-empty description="暂无数据" image-size={100}></el-empty>
        ) : undefined}
        <proPagination
          style="margin-top: 16px"
          pagination={{
            currentChange: (val) => {
              this.pagination = { ...this.pagination, currentPage: val };
              if (this.paginationProps.currentChange)
                this.paginationProps.currentChange(val);
              this.onSearch();
            },
            sizeChange: (val) => {
              this.pagination = { ...this.pagination, pageSize: val };
              if (this.paginationProps.sizeChange)
                this.paginationProps.sizeChange(val);
              this.onSearch();
            },
            ...this.pagination,
            ...this.paginationProps,
          }}
          resetPagination={() => {
            this.pagination = { ...this.pagination, currentPage: 1 };
            if (this.paginationProps.currentChange)
              this.paginationProps.currentChange(1);
          }}
        />
      </div>
    );
  },
};
</script>
<style lang="scss" scoped>
::v-deep .el-table {
  ::-webkit-scrollbar {
    width: 6px !important;
    height: 6px !important;
  }
  ::-webkit-scrollbar-track {
    box-shadow: inset 0 0 6px rgba(243, 245, 248, 1);
    border-radius: 10px;
    background-color: #f5f5f5;
  }

  /*定义滑块，内阴影及圆角*/
  ::-webkit-scrollbar-thumb {
    height: 10px;
    border-radius: 10px;
    background-color: rgba(173, 181, 192, 1);
  }

  html::-webkit-scrollbar {
    height: 0;
    width: 0 !important;
  }
}
.proTableOtherRenders {
  margin: 24px 0;
}
.proTableToolbar {
  margin: 24px 0;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
</style>
