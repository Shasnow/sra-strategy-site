<template>
  <el-dialog
    :model-value="visible"
    title="攻略详情"
    :width="dialogWidth"
    @update:model-value="emit('close')"
  >
    <el-descriptions :column="1" border>
      <el-descriptions-item label="标题">{{ strategy.title }}</el-descriptions-item>
      <el-descriptions-item label="描述">{{ strategy.description }}</el-descriptions-item>
      <el-descriptions-item label="作者">{{ strategy.author }}</el-descriptions-item>
      <el-descriptions-item label="上传者">{{ strategy.uploader }}</el-descriptions-item>
      <el-descriptions-item label="分享码">{{ strategy.share_code }}</el-descriptions-item>
      <el-descriptions-item label="最低金币">{{ strategy.min_coins }}</el-descriptions-item>
      <el-descriptions-item label="最低等级">{{ strategy.min_level }}</el-descriptions-item>
      <el-descriptions-item label="中期等级">{{ strategy.mid_level }}</el-descriptions-item>
      <el-descriptions-item label="上场角色">{{ strategy.on_field }}</el-descriptions-item>
      <el-descriptions-item label="后台角色">{{ strategy.off_field }}</el-descriptions-item>
    </el-descriptions>
    <template #footer>
      <span class="dialog-footer">
        <el-button @click="emit('close')">关闭</el-button>
        <el-button type="primary" @click="emit('download', strategy.id)">下载</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<script setup>
import { computed } from 'vue';

defineProps({
  visible: {
    type: Boolean,
    required: true,
  },
  strategy: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(['close', 'download']);

const dialogWidth = computed(() => {
  return window.innerWidth <= 640 ? '92%' : '560px';
});
</script>

<style scoped>
.dialog-footer {
  display: flex;
  gap: 0.5rem;
  justify-content: flex-end;
}
</style>