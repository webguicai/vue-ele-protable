<template>
  <el-form :model="formData" ref="form" @submit.native.prevent="handleSubmit">
    <el-form-item v-for="item in formItems" :key="item.field" :label="item.label" :prop="item.field">
      <el-form-component :component="item.component" :props="item.componentProps"></el-form-component>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" native-type="submit">提交</el-button>
    </el-form-item>
  </el-form>
</template>

<script>
import { defineComponent } from 'vue';

export default defineComponent({
  props: {
    formItems: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      formData: {},
    };
  },
  methods: {
    handleSubmit() {
      this.$refs.form.validate((valid) => {
        if (valid) {
          // 处理表单提交
          console.log(this.formData);
        }
      });
    },
  },
});
</script>
