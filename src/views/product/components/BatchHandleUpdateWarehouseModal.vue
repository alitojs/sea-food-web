<template>
    <Modal  v-model:open="isVisible" destroyOnClose title="选择修改仓库" :width="500" @ok="handleSubmit">
    	<j-dict-select-tag type='select' v-model:value="wareHouseValue" dictCode="tb_warehouse_info, name, id" placeholder="请选择仓库"  allow-clear />
    </Modal> 
</template>
<script lang="ts" setup>
import { Modal } from 'ant-design-vue';
import { ref } from 'vue';
import { message } from 'ant-design-vue';
import { batchEditWaveHouseByProductSpecificationApi } from '../ProductInfo.api';
import JDictSelectTag from '/@/components/Form/src/jeecg/components/JDictSelectTag.vue';
const emit = defineEmits(['submit']);

// 定义模态框显示状态的响应式变量
const isVisible = ref(false);
const selIds = ref([]);
const wareHouseValue=ref(undefined);
  function handleSubmit() {
    if(!wareHouseValue.value) {
      message.error('请选择');
      return ;
    }
    const params = {
      waveHouseId: wareHouseValue.value,
      productInfoIds: selIds.value.join(',')
    }
    batchEditWaveHouseByProductSpecificationApi(params).then(() => {
      isVisible.value = false;
      emit('submit');
    })
  }

  const open = ({ids}) => {
    selIds.value = ids;
    isVisible.value = true;
};

defineExpose({
    open
});

</script>
