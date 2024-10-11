<template>
	<div class="container">
		<el-dialog :model-value="dialogVisible" width="25%" :before-close="handleClose">
			<template #header>
				<h3>{{ '任务详情' }}</h3>
			</template>
			<!-- 具体内容 -->
			<el-descriptions :column="1" border>
				<template #title>
					<div class="task-title">{{ taskTitle }}</div>
				</template>
				<el-descriptions-item label="执行人" min-width="90">{{ taskContent.name }}</el-descriptions-item>
				<el-descriptions-item label="岗位">{{ taskContent.job }}</el-descriptions-item>
				<el-descriptions-item label="执行时间">{{ taskStartTime }} - {{ taskEndTime }}</el-descriptions-item>
				<el-descriptions-item label="描述"> {{ taskContent.description }} </el-descriptions-item>
				<el-descriptions-item label="创建时间"> </el-descriptions-item>
				<el-descriptions-item label="创建人"> </el-descriptions-item>
			</el-descriptions>
		</el-dialog>
	</div>
</template>
<script setup lang="ts">
import { onMounted, ref, watch } from 'vue';
import dayjs from 'dayjs';

// 接收父组件传值
const props = defineProps(['dialogVisible', 'detailInfo']);
const emits = defineEmits(['update:dialogVisible']);

const taskTitle = ref(''); // 任务标题
const taskContent = ref<any>('');
const taskStartTime = ref(''); // 任务开始时间
const taskEndTime = ref(''); // 任务结束时间

watch(
	() => props.detailInfo,
	(newValue) => {
		console.log(newValue);
		taskTitle.value = newValue.title;
		taskContent.value = newValue.extendedProps;
		taskStartTime.value = dayjs(newValue.start).format('YYYY-MM-DD HH:mm:ss');
		taskEndTime.value = dayjs(newValue.end).format('YYYY-MM-DD HH:mm:ss');
	}
);
/**
 * 弹窗关闭时操作
 */
const handleClose = () => {
	emits('update:dialogVisible', false);
};
</script>
<style lang="scss" scoped>
.task-title::before{
  content: '';
  width: 6px;
  height: 22px;
  display: inline-block;
  vertical-align: middle;
  background: #409eff;
  margin-right: 8px;
  border-radius: 10px;
}
.task-title{
  vertical-align: middle;
}
:deep(.el-overlay .el-overlay-dialog .el-dialog .el-dialog__body) {
	padding: 0 !important;
}
:deep(.el-overlay) {
	z-index: 9999 !important;
}
</style>
