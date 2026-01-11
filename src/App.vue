<script setup>
import { ref, computed, onMounted } from 'vue';

const userItems = ref([]);
const rightItems = ref([]);

const selectedUserItems = ref([]);
const selectedRightItem = ref(null);

const loading = ref(true);

const isUserItemSelected = (id) => {
  return selectedUserItems.value.some(item => item.id === id);
};

const toggleUserItem = (item) => {
  const index = selectedUserItems.value.findIndex(i => i.id === item.id);
  if (index !== -1) {
    selectedUserItems.value.splice(index, 1);
  } else if (selectedUserItems.value.length < 6) {
    selectedUserItems.value.push(item);
  }
};

const selectRightItem = (item) => {
  if (selectedRightItem.value?.id === item.id) {
    selectedRightItem.value = null;
  } else {
    selectedRightItem.value = item;
  }
};

const hasSelectedUserItems = computed(() => selectedUserItems.value.length > 0);

onMounted(async () => {
  try {
    const response = await fetch('/data.json');
    const data = await response.json();
    userItems.value = data.userItems;
    rightItems.value = data.rightItems;
  } catch (error) {
    console.error('Data loading error:', error);
  } finally {
    loading.value = false;
  }
});
</script>

<template>
  <div class="item-selector">
    <div class="item-selector__top-section">
      <div class="item-selector__top-column">
        <div class="item-selector__selected-items"
             :class="{ 'item-selector__selected-items--empty': !hasSelectedUserItems }">
          <div v-for="item in selectedUserItems"
               :key="'selected-' + item.id"
               class="item-selector__selected-item">
            {{ item.name }}
          </div>
          <div v-if="!hasSelectedUserItems" class="item-selector__empty-placeholder">
            Items not selected
          </div>
        </div>
        <div class="item-selector__selected-counter">
          selected: {{ selectedUserItems.length }}/6
        </div>
      </div>

      <div class="item-selector__top-column">
        <div class="item-selector__selected-main-item">
          <div v-if="selectedRightItem" class="item-selector__selected-item item-selector__selected-item--main">
            {{ selectedRightItem.name }}
          </div>
          <div v-else class="item-selector__empty-placeholder">
            Item not selected
          </div>
        </div>
        <div class="item-selector__selected-counter">
          selected: {{ selectedRightItem ? '1 / 1' : '0 / 1' }}
        </div>
      </div>
    </div>

    <div class="item-selector__bottom-section">
      <div class="item-selector__bottom-column">
        <div class="item-selector__items-grid">
          <div v-for="item in userItems"
               :key="'user-' + item.id"
               :class="['item-selector__item-card', { 'item-selector__item-card--selected': isUserItemSelected(item.id) }]"
               @click="toggleUserItem(item)">
            {{ item.name }}
          </div>
        </div>
      </div>

      <div class="item-selector__bottom-column">
        <div class="item-selector__items-grid">
          <div v-for="item in rightItems"
               :key="'right-' + item.id"
               :class="['item-selector__item-card', { 'item-selector__item-card--selected item-selector__item-card--selected-main': selectedRightItem?.id === item.id }]"
               @click="selectRightItem(item)">
            {{ item.name }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.item-selector {
  padding: 20px;
  max-width: 1200px;
  margin: 0 auto;
  font-family: Arial, sans-serif;
}

.item-selector__top-section {
  display: flex;
  gap: 20px;
  margin-bottom: 40px;
  border-bottom: 2px solid #ccc;
  padding-bottom: 20px;
}

.item-selector__top-column {
  flex: 1;
  min-height: 150px;
  padding: 20px;
  border: 2px dashed #aaa;
  border-radius: 8px;
}

.item-selector__bottom-section {
  display: flex;
  gap: 20px;
}

.item-selector__bottom-column {
  flex: 1;
  padding: 20px;
  border: 2px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
}

.item-selector__selected-items {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}

.item-selector__selected-items--empty {
  display: block;
}

.item-selector__selected-item {
  padding: 10px 15px;
  border: 2px solid #4CAF50;
  border-radius: 6px;
  background-color: #e8f5e9;
  min-width: 80px;
  text-align: center;
}

.item-selector__selected-item--main {
  border-color: #2196F3;
  background-color: #e3f2fd;
  font-size: 18px;
  padding: 20px;
}

.item-selector__empty-placeholder {
  padding: 20px;
  border: 2px dashed #ccc;
  border-radius: 6px;
  color: #888;
  text-align: center;
  font-style: italic;
}

.item-selector__selected-counter {
  margin-top: 15px;
  font-size: 14px;
  color: #666;
  text-align: center;
}

.item-selector__items-grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
  margin-top: 10px;
}

.item-selector__item-card {
  padding: 15px;
  border: 2px solid #ddd;
  border-radius: 6px;
  background-color: white;
  text-align: center;
  cursor: pointer;
  transition: all 0.2s;
}

.item-selector__item-card:hover {
  border-color: #aaa;
  background-color: #f0f0f0;
}

.item-selector__item-card--selected {
  border-color: #4CAF50;
  background-color:#e8f5e9;
  font-weight: bold;
}
.item-selector__item-card--selected.item-selector__item-card--selected-main{
  border-color: #2196F3;
  background-color: #e3f2fd;
}
</style>
