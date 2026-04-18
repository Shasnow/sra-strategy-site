<script setup>
import { ref } from 'vue';

defineProps({
  meta: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(['show-detail', 'download']);

const loadingDetail = ref(false);
const loadingDownload = ref(false);

const handleDetail = async (id) => {
  if (loadingDetail.value) return;
  loadingDetail.value = true;
  try {
    await new Promise((resolve) => emit('show-detail', id, resolve));
  } finally {
    loadingDetail.value = false;
  }
};

const handleDownload = async (id) => {
  if (loadingDownload.value) return;
  loadingDownload.value = true;
  try {
    await new Promise((resolve) => emit('download', id, resolve));
  } finally {
    loadingDownload.value = false;
  }
};
</script>

<template>
  <el-card class="strategy-card">
    <div class="card-inner">
      <div class="card-content">
        <h3 class="card-title">{{ meta.title }}</h3>
        <p class="card-desc">{{ meta.description || '暂无描述' }}</p>
      </div>
      <div class="card-actions">
        <el-tooltip content="查看详情" placement="top">
          <button class="icon-btn" :class="{ loading: loadingDetail }" :disabled="loadingDetail" @click="handleDetail(meta.id)">
            <svg v-if="!loadingDetail" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><line x1="12" y1="16" x2="12" y2="12"/><line x1="12" y1="8" x2="12.01" y2="8"/></svg>
            <svg v-else class="spin" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12a9 9 0 1 1-6.219-8.56"/></svg>
          </button>
        </el-tooltip>
        <el-tooltip content="下载" placement="top">
          <button class="icon-btn icon-btn--primary" :class="{ loading: loadingDownload }" :disabled="loadingDownload" @click="handleDownload(meta.id)">
            <svg v-if="!loadingDownload" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/></svg>
            <svg v-else class="spin" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21 12a9 9 0 1 1-6.219-8.56"/></svg>
          </button>
        </el-tooltip>
      </div>
    </div>
  </el-card>
</template>

<style scoped>
.strategy-card {
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

.strategy-card:hover {
  transform: translateY(-2px);
  box-shadow: var(--box-shadow-hover) !important;
  border-color: rgba(124, 158, 245, 0.4) !important;
}

.card-inner {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.card-content {
  flex: 1;
  min-width: 0;
}

.card-title {
  font-size: 0.95rem;
  font-weight: 600;
  color: var(--text-color);
  margin: 0 0 0.3rem 0;
  line-height: 1.4;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.card-desc {
  font-size: 0.82rem;
  color: var(--text-muted);
  line-height: 1.5;
  margin: 0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.card-actions {
  display: flex;
  gap: 0.4rem;
  flex-shrink: 0;
}

.icon-btn {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 34px;
  height: 34px;
  border-radius: 8px;
  border: 1px solid var(--border-color);
  background: transparent;
  color: var(--text-muted);
  cursor: pointer;
  transition: all 0.15s ease;
  padding: 0;
}

.icon-btn svg {
  width: 16px;
  height: 16px;
}

.icon-btn:hover {
  color: var(--text-color);
  border-color: rgba(124, 158, 245, 0.5);
  background: rgba(124, 158, 245, 0.08);
}

.icon-btn--primary {
  color: var(--primary-color);
  border-color: rgba(124, 158, 245, 0.3);
}

.icon-btn--primary:hover {
  background: rgba(124, 158, 245, 0.15);
  border-color: var(--primary-color);
  color: var(--primary-color);
}

.icon-btn:disabled {
  cursor: not-allowed;
  opacity: 0.7;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.spin {
  animation: spin 0.8s linear infinite;
}
</style>