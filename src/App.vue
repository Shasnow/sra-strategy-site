<script setup>
import { ref, onMounted } from 'vue';
import { ElMessage } from 'element-plus';
import { getStrategies, getStrategyDetail, createStrategy } from '@/api/strategy.js';
import StrategyCard from './components/StrategyCard.vue';
import StrategyDetailModal from './components/StrategyDetailModal.vue';
import UploadModal from './components/UploadModal.vue';

const strategyMetas = ref([]);
const strategyDetail = ref({});
const strategyDetailVisible = ref(false);
const uploadDialogVisible = ref(false);

const loadStrategies = async () => {
  try {
    const res = await getStrategies();
    strategyMetas.value = res?.data || [];
  } catch (err) {
    strategyMetas.value = [];
    console.error('Failed to fetch strategies:', err);
  }
};

const showStrategyDetail = async (id, done) => {
  try {
    const res = await getStrategyDetail(id);
    strategyDetail.value = res.data;
    strategyDetailVisible.value = true;
  } catch (err) {
    console.error('Failed to fetch strategy detail:', err);
  } finally {
    done?.();
  }
};

const downloadStrategy = async (id, done) => {
  try {
    const res = await getStrategyDetail(id);
    const strategy = res.data;
    downloadJsonFile(strategy, `SRAstrategy_${strategy.title || id}.json`);
  } catch (err) {
    console.error('Failed to download strategy:', err);
  } finally {
    done?.();
  }
};

const handleUploadSuccess = async (parsedData) => {
  try {
    await createStrategy(parsedData);
    await loadStrategies();
  } catch (err) {
    console.error('Failed to upload strategy:', err);
  }
};

const downloadJsonFile = (data, fileName) => {
  const json = JSON.stringify(data, null, 2);
  const blob = new Blob([json], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = fileName;
  a.click();
  URL.revokeObjectURL(url);
};

onMounted(loadStrategies);
</script>

<template>
  <div class="common-layout">
    <el-container>
      <el-header class="header">
        <div class="header-left">
          <img src="/favicon.ico" alt="logo" class="header-logo" />
          <div class="header-title">
            <h1>StarRailAssistant 攻略站</h1>
            <span class="header-subtitle">货币战争攻略</span>
          </div>
        </div>
        <el-button type="primary" @click="uploadDialogVisible = true">上传攻略</el-button>
      </el-header>
      <el-main>
        <div v-if="strategyMetas.length > 0" class="strategy-grid">
          <StrategyCard
            v-for="meta in strategyMetas"
            :key="meta.id"
            :meta="meta"
            @show-detail="showStrategyDetail"
            @download="downloadStrategy"
          />
        </div>
        <el-card v-else class="no-strategies">
          <p>暂无攻略，快来上传第一篇吧</p>
        </el-card>
      </el-main>
    </el-container>

    <StrategyDetailModal
      :visible="strategyDetailVisible"
      :strategy="strategyDetail"
      @close="strategyDetailVisible = false"
      @download="downloadStrategy"
    />

    <UploadModal
      :visible="uploadDialogVisible"
      @close="uploadDialogVisible = false"
      @upload-success="handleUploadSuccess"
    />
  </div>
</template>

<style scoped>
.common-layout {
  max-width: 1200px;
  margin: 0 auto;
  height: 100vh;
  display: flex;
  flex-direction: column;
  overflow: hidden;
  padding: 1.5rem;
  box-sizing: border-box;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding: 1.25rem 1.5rem;
  background: var(--background-card);
  border: 1px solid var(--border-color);
  border-radius: var(--border-radius);
  box-shadow: var(--box-shadow);
  height: auto !important;
  flex-shrink: 0;
}

.header-left {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.header-logo {
  width: 36px;
  height: 36px;
  object-fit: contain;
}

.header-title {
  display: flex;
  flex-direction: column;
}

.header h1 {
  color: var(--primary-color);
  font-size: 1.5rem;
  font-weight: 700;
  margin: 0;
  line-height: 1.2;
  letter-spacing: 0.02em;
  text-shadow: 0 0 20px var(--primary-glow);
}

.header-subtitle {
  font-size: 0.75rem;
  color: var(--text-muted);
  margin-top: 2px;
}

.strategy-grid {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

:deep(.el-main) {
  flex: 1;
  overflow-y: auto;
  padding: 0.75rem 0 0 0;
}

.no-strategies {
  text-align: center;
  padding: 4rem 2rem;
  color: var(--text-muted);
}

.no-strategies p {
  font-size: 1rem;
  margin: 0;
}

/* 移动端适配 */
@media (max-width: 640px) {
  .header {
    padding: 1rem;
    margin-bottom: 1.25rem;
  }

  .header h1 {
    font-size: 1.1rem;
  }

  .header-subtitle {
    display: none;
  }

  .strategy-grid {
    gap: 0.625rem;
  }
}
</style>
