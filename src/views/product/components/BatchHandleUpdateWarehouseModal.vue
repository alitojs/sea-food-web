<template>
    <Modal  v-model:visible="isVisible" destroyOnClose title="选择修改仓库" :width="500" @ok="handleSubmit">
    </Modal> 
</template>
<script lang="ts" setup>
import { Modal } from 'ant-design-vue';
import { ref } from 'vue';

// 定义模态框显示状态的响应式变量
const isVisible = ref(false);
  function handleSubmit() {
    console.log('submit');
  }

  const open = () => {
    console.log(123123);
    isVisible.value = true;
};

defineExpose({
    open
});

</script>
