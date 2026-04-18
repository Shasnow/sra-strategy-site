<script setup>
import { ref, computed } from 'vue';
import { ElMessage } from 'element-plus';

const props = defineProps({
  visible: {
    type: Boolean,
    required: true,
  },
});

const emit = defineEmits(['close', 'upload-success']);

const uploadLoading = ref(false);
const uploadFileName = ref('');

const beforeJsonUpload = (file) => {
  const isJson = file.type === 'application/json' || file.name.toLowerCase().endsWith('.json');
  if (!isJson) {
    ElMessage.error('请上传 JSON 文件');
  }
  return isJson;
};

const readFileText = (file) =>
  new Promise((resolve, reject) => {
    const reader = new FileReader();
    reader.onload = () => resolve(String(reader.result ?? ''));
    reader.onerror = () => reject(new Error('读取文件失败'));
    reader.readAsText(file, 'utf-8');
  });

const handleJsonFileChange = async (uploadFile) => {
  const file = uploadFile?.raw;
  if (!file) return;

  uploadFileName.value = file.name;
  uploadLoading.value = true;

  try {
    const text = await readFileText(file);
    const parsed = JSON.parse(text);
    emit('upload-success', parsed);
    closeDialog();
  } catch (err) {
    console.error('Failed to process file:', err);
    ElMessage.error(err instanceof SyntaxError ? 'JSON 格式不正确' : '处理文件失败');
  } finally {
    uploadLoading.value = false;
  }
};

const closeDialog = () => {
  emit('close');
  uploadFileName.value = '';
};

const dialogWidth = computed(() => {
  return window.innerWidth <= 640 ? '92%' : '480px';
});
</script>

<template>
  <el-dialog
    :model-value="visible"
    title="上传攻略"
    :width="dialogWidth"
    @update:model-value="closeDialog"
  >
    <el-upload
      :auto-upload="false"
      :before-upload="beforeJsonUpload"
      :show-file-list="false"
      accept=".json,application/json"
      action="#"
      drag
      @change="handleJsonFileChange"
    >
      <div class="upload-area">
        <div class="el-upload__text">将 JSON 文件拖到此处，或 <em>点击选择</em></div>
        <div class="upload-hint">.json 格式</div>
      </div>
    </el-upload>

    <div v-if="uploadFileName" class="file-selected">
      <span>已选择：{{ uploadFileName }}</span>
    </div>
    <p class="upload-notice">上传后需联系管理员审核，才能公开可见</p>
    <template #footer>
      <span class="dialog-footer">
        <el-button :disabled="uploadLoading" @click="closeDialog">取消</el-button>
      </span>
    </template>
  </el-dialog>
</template>

<style scoped>
.upload-area {
  padding: 1.5rem 1rem;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.5rem;
}

.upload-hint {
  font-size: 0.75rem;
  color: var(--text-muted);
}

.file-selected {
  margin-top: 0.75rem;
  font-size: 0.875rem;
  color: var(--primary-color);
}

.upload-notice {
  margin-top: 0.5rem;
  font-size: 0.8rem;
  color: var(--text-muted);
}

.dialog-footer {
  display: flex;
  justify-content: flex-end;
}
</style>