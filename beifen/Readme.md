## 作用：
#### 生成一个中后台常见的搜索页面，根据tableColumns直接生成searchForm table pagination



## 安装:

```javascript
// npm
npm i vue-ele-protable -D

//yarn 
yarn add vue-ele-protable

```

## 快速使用:

```javascript
// 在 main.js中全局使用，或者可在单独文件中注入

import VueEleProtable from 'vue-ele-protable'
import "vue-ele-protable/vueProtable.css"
import Vue from 'vue'

Vue.use(VueEleProtable);
```
## 简单案例:

```vue
<template>
  <div class="examplePage">
      <!-- 
          protableRef     绑定当前父组件data中的接受 ref 的键，传入string类型
必传       tableColumns    table 及 form 的配置
必传       tableData       table的数据
          formProps       form的所有属性 attrs都可写入
          tableProps      table的所有属性 attrs都可写入
必传总页数  paginationProps pagination的所有属性 attrs都可写入 ， 至少传入，其余不必须！！！！！！！！！！！！！！！！！！
          toolbar         工具栏渲染
必传       v-model         绑定搜索栏的数据对象
必传       @onSearch       抛出重置及搜索按钮事件
          loading         传入boolean，判断是否为数据获取中（调接口）
          customTable     传入回调函数(Node, tableProps, tableColumns) => { return Node }，可实现完全自定义table，Node为本身组件table节点
          @******         el-table自带的所有事件都可以透传获取执行 , 查看element-ui Table Events : [Title](https://element.eleme.cn/#/zh-CN/component/table)
      -->
    <vue-ele-protable
      protableRef="protableRef" 
      :tableColumns="tableColumns"
      :tableData="tableData"
      :formProps="{ labelSuffix: ' :' }"
      :paginationProps="{ total: 100 }"
      :otherRenders="otherRenders"
      :toolbar="toolbar"
      v-model="proFormData"
      @onSearch="onSearch"
      :loading="loading"
    />
  </div>
</template>
<script>

export default {
  data() {
    return {
      loading: false,
      protableRef: {},
      proFormData: {},
      otherRenders: () => {
        return (
          <h1>标题</h1>
        )
      },
      toolbar: {
        left: [
          <el-button type="primary">新增</el-button>,
          <el-button type="success">批量导出</el-button>,
        ],
        right: [<el-button>全部导出</el-button>],
      },
      // 表格列配置，
      // 大部分属性跟 el-table-column 及 el-form-item 配置一样，可参考element-ui官网，单独想配置都放入其中
      // 比如: form的label宽: labelWidth: '200px', 对应列的宽度: width: 100, 列是否固定在左侧或者右侧，true 表示固定在左侧: fixed: true

      tableColumns: [
        {
          type: "selection",
          width: 55,
          hideInSearch: true,
        },
        {
          label: "id",
          prop: "id",
          placeholder: "请输入",
          hideInTable: true
        },
        {
          label: "姓名",
          prop: "name",
          placeholder: "请输入",
        },
        {
          label: "性别",
          prop: "gender",
          render: ({ row }) => {
            return row.gender === '1' ? '男' : row.gender === '2' ? '女' : '未知'
          },
          renderItem: () => {
            return (
              <el-select v-model={this.proFormData.gender}>
                <el-option label="男" value="1"></el-option>
                <el-option label="女" value="2"></el-option>
                <el-option label="未知" value="0"></el-option>
              </el-select>
            );
          },
        },
        {
          label: "操作",
          prop: "option",
          hideInSearch: true,
          render: (record) => {
            return (
              <el-button
                type="text"
                onClick={() => {
                  console.log(record);
                }}
              >
                编辑
              </el-button>
            );
          },
        },
      ],
      tableData: [
        { name: "男男", gender: "1", id: "1" },
        { name: "女女", gender: "2", id: "2" },
        { name: "未知", gender: "0", id: "0" },
      ],
    };
  },
  methods: {
    onSearch(params) {
      console.log(params);
    },
  },
};
</script>
<style lang="scss" scoped>
.examplePage {
  width: 100%;
  height: 100%;
  background-color: #fff;
  box-sizing: border-box;
  display: flex;
  flex-wrap: wrap;
}
</style>

```
## 预览效果

 ![image](![https://github.com/ButBueatiful/dotvim/raw/master/screenshots/vim-screenshot.jpg](https://raw.githubusercontent.com/webguicai/vue-ele-protable/main/beifen/image.png))

## 表格配置

### 具体参数可查看element-ui ，下方罗列部分
    table: Table Attributes [Title](https://element.eleme.cn/#/zh-CN/component/table#table-attributes)
    form:  Form Attributes  [Title](https://element.eleme.cn/#/zh-CN/component/form#form-attributes)


| 参数      | 说明                                                                                        | 类型                   | 可选值                 | 默认值 |
| --------- | ------------------------------------------------------------------------------------------- | ---------------------- | ---------------------- | ------ |
| label     | 对应 el-table-column 的 label                                                               | string                 | -                      | -      |
| type      | 对应 el-table-column 的 type                                                                | string                 | selection/index/expand | -      |
| prop      | 对应 el-table-column 的 prop                                                                | string                 | -                      | -      |
| width     | 对应 el-table-column 的 width                                                               | string,number          | -                      | -      |
| hideInSearch  | 是否在搜索栏中隐藏                                                                        | boolean                | -                      | false      |
| hideInTable  | 是否在表格中隐藏                                                                        | boolean                | -                      | false      |
| align     | 对应 el-table-column 的 align                                                               | string                 | left/center/right      | left   |
| fixed     | 对应 el-table-column 的 fixed                                                               | string, boolean        | true, left, right      | -      |