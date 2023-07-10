<template>
  <div>
    <pro-form :formItems="formItems" />
  </div>
</template>

<script>
import ProForm from './ProForm.vue';
import { ElInput, ElInputNumber } from 'element-plus';

export default {
  components: {
    ProForm,
    ElInput,
    ElInputNumber,
  },
  data() {
    return {
      formItems: [
        {
          label: '用户名',
          field: 'username',
          component: 'ElInput',
          componentProps: {
            placeholder: '请输入用户名',
          },
        },
        {
          label: '年龄',
          field: 'age',
          component: 'ElInputNumber',
          componentProps: {
            placeholder: '请输入年龄',
          },
        },
      ],
    };
  },
};
</script>
