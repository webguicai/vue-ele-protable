<script>
export default {
  name: 'pro-form',
  props: {
    searchItem: { default: () => [] },
    labelWidth: { default: "144px" },
    formProps: { default: () => {} },
    proFormData: { default: () => {}, required: true },
    proFormRef: { default: '' }
  },
  data() {
    return {};
  },
  mounted() {
    this.$parent[proFormRef] = this.$refs.elForm;
  },
  render() {
    return (
      <el-form
        value={this.proFormData}
        inline={true}
        class="demo-form-inline"
        label-width={this.labelWidth}
        ref="elForm"
        attrs={{ ...this.formProps }}
      >
        {this.searchItem.map((item) => {
          const { render, renderItem, ...others } = item;
          if (item.show === false || item.hideInSearch) {
            return;
          } else {
            return (
              <el-form-item
                attrs={{ style: `--labelWidth: ${this.labelWidth}`, ...others }}
              >
                {item.renderItem ? (
                  item.renderItem
                ) : (
                  <el-input
                    size={
                      this.formProps?.size ? this.formProps?.size : "default"
                    }
                    value={this.proFormData[item.prop]}
                    onInput={(v) => {
                      this.proFormData[item.prop] = v;
                    }}
                    placeholder="请输入"
                  />
                )}
              </el-form-item>
            );
          }
        })}
        <el-form-item class="formBtns">
          {(this.formProps?.renderBtns || []).length > 0
            ? this.formProps?.renderBtns?.map((item) => item)
            : [
                <el-button
                  onClick={() => {
                    this.$refs.elForm.resetFields();
                    this.formProps?.onReset();
                  }}
                >
                  重置
                </el-button>,
                <el-button
                  type="primary"
                  onClick={() => {
                    this.formProps?.onSearch();
                  }}
                >
                  查询
                </el-button>,
              ]}
        </el-form-item>
        <div style="clear: both"></div>
      </el-form>
    );
  },
};
</script>

<style lang="scss" scoped>
::v-deep .el-form-item {
  margin-right: 0px;
  width: 25%;
  display: inline-flex;
  align-items: center;
}
::v-deep .el-form-item__content {
  width: 100%;
}
::v-deep .el-form-item__content > div {
  width: 100%;
}
.formBtns {
  padding-top: 4px;
  padding-right: 0px;
  float: right;
  margin-right: 0;
  ::v-deep .el-form-item__content {
    display: flex;
    justify-content: flex-end;
    align-items: center;
  }
}
</style>
