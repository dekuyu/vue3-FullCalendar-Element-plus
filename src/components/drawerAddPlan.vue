<template>

  <div class="container">
    <el-drawer :model-value="drawerVisile" :before-close="handleClose" :close-on-click-modal="false">
      <template #header>
        <span class="title">新增排班</span>
      </template>
    </el-drawer>
  </div>
  
  </template>
  <script setup lang='ts'>
  import {ref,reactive} from 'vue'
  
  const props = defineProps(['drawerVisile'])
  const emits = defineEmits(['update:drawerVisile'])
  /**
   * 关闭抽屉
   */
  const handleClose = () => {
    emits('update:drawerVisile', false);
    console.log('close')
  }
  </script>
  <style scoped>
  
  </style>