<template>
  <div>
    <!--引用表格-->
    
   <BasicTable @register="registerTable" :rowSelection="rowSele·ction" :expandedRowKeys="expandedRowKeys"  @expand="handleExpand">
           <!--操作栏-->
           <template #action="{ record }">
        <TableAction :actions="showAction(record)" />
      </template>
      <!--字段回显插槽-->
      <template v-slot:bodyCell="{ column, record, index, text }">
      </template>
    </BasicTable>
    <!-- 表单区域 -->
    <ProductInfoModal @register="registerModal" @success="handleSuccess"></ProductInfoModal>
  </div>
</template>

<script lang="ts" name="product-productInfo" setup>
  import {ref, reactive, computed, unref} from 'vue';
  import {BasicTable, useTable, TableAction} from '/@/components/Table';
  import { useListPage } from '/@/hooks/system/useListPage'
  import {useModal} from '/@/components/Modal';
  import ProductInfoModal from './components/ProductInfoModal.vue'
  import ProductSpecificationSubTable from './subTables/ProductSpecificationSubTable.vue'
  import {columns, searchFormSchema, superQuerySchema} from './ProductInfo.data';
  import {list, deleteOne, batchDelete, getImportUrl,getExportUrl} from './ProductInfo.api';
  import {downloadFile} from '/@/utils/common/renderUtils';
  import { useUserStore } from '/@/store/modules/user';
  const queryParam = reactive<any>({});
   // 展开key
  const expandedRowKeys = ref<any[]>([]);
  //注册model
  const [registerModal, {openModal}] = useModal();
  const userStore = useUserStore();
   //注册table数据
  const { prefixCls,tableContext,onExportXls,onImportXls } = useListPage({
      tableProps:{
           title: '商品信息表',
           api: list,
           columns,
           canResize:false,
           formConfig: {
                //labelWidth: 120,
                schemas: searchFormSchema,
                autoSubmitOnEnter:true,
                showAdvancedButton:true,
                fieldMapToNumber: [
                ],
                fieldMapToTime: [
                ],
            },
           actionColumn: {
               width: 120,
               fixed:'right'
           },
           beforeFetch: (params) => {
             return Object.assign(params, queryParam);
           },
        },
        exportConfig: {
            name:"商品信息表",
            url: getExportUrl,
            params: queryParam,
        },
        importConfig: {
            url: getImportUrl,
            success: handleSuccess
        },
    })

  const [registerTable, {reload},{ rowSelection, selectedRowKeys }] = tableContext

   /**
     * 展开事件
     * */
   function handleExpand(expanded, record){
        expandedRowKeys.value=[];
        if (expanded === true) {
           expandedRowKeys.value.push(record.id)
        }
    }
   /**
    * 新增事件
    */
  function handleAdd() {
     openModal(true, {
       isUpdate: false,
       showFooter: true,
     });
  }
   /**
    * 编辑事件
    */
  function handleEdit(record: Recordable) {
     openModal(true, {
       record,
       isUpdate: true,
       showFooter: true,
     });
   }
   /**
    * 详情
   */
  function handleDetail(record: Recordable) {
     openModal(true, {
       record,
       isUpdate: true,
       showFooter: false,
     });
   }
   /**
    * 删除事件
    */
  async function handleDelete(record) {
     await deleteOne({id: record.id}, handleSuccess);
   }
   /**
    * 批量删除事件
    */
  async function batchHandleDelete() {
     await batchDelete({ids: selectedRowKeys.value},handleSuccess);
   }
   /**
    * 成功回调
    */
  function handleSuccess() {
      (selectedRowKeys.value = []) && reload();
   }
   /**
      * 操作栏
      */
  function getTableAction(record){
       return [
         {
           label: '编辑',
           onClick: handleEdit.bind(null, record),
         }
       ]
   }


  /**
   * 下拉操作栏
   */
  function getDropDownAction(record){
    return [
      {
        label: '详情',
        onClick: handleDetail.bind(null, record),
      }, {
        label: '删除',
        popConfirm: {
          title: '是否确认删除',
          confirm: handleDelete.bind(null, record),
          placement: 'topLeft',
        }
      }
    ]
  }


    /**
   * 下拉操作栏
   */
   function showAction(record){
    return [
      {
        label: '详情',
        onClick: handleDetail.bind(null, record),
      }
    ]
  }

</script>

<style scoped>
  :deep(.ant-picker),:deep(.ant-input-number){
    width: 100%;
  }
</style>