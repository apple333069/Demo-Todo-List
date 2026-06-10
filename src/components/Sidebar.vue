<script setup>
const props = defineProps({ 
  items: Array, 
  activeIndex: Number 
})

const emit = defineEmits(['add','select','delete','close'])

// 發送 'add' 事件告知父元件
const onAdd = () => { emit('add') }
// 發送 'select' 事件並帶入選取的項目索引
const onSelect = (i) => { emit('select', i) }
// 手機版關閉側邊欄時，發送 'close' 事件告知父元件
const onClose = () => { emit('close') }
</script>

<template>
  <div class="sidebar-wrap h-full flex flex-col py-6 relative">
    <!-- Demo Todo List 主標題 -->
    <div class="flex items-center justify-between mb-6 px-6">
      <strong class="font-medium text-black">Demo Todo List</strong>
      <button class="hidden max-[720px]:flex bg-transparent border-none cursor-pointer p-1 text-black items-center justify-center" @click="onClose" title="Close menu">
        <svg width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
          <line x1="18" y1="6" x2="6" y2="18"></line>
          <line x1="6" y1="6" x2="18" y2="18"></line>
        </svg>
      </button>
    </div>

    <!-- 顯示所有的待辦項目列表 -->
    <nav class="overflow-y-auto mb-4 scrollbar-none max-h-[300px]">
      <ul class="list-none p-0 m-0">
        <li 
          v-for="(it, idx) in items" 
          :key="idx" 
          @click="onSelect(idx)" 
          :class="[
            activeIndex === idx ? 'font-bold active-notch' : 'font-regular',
            'px-6 py-4 cursor-pointer relative bg-mint-dark text-black mb-1.5 hover:opacity-95 transition-all duration-200'
          ]"
        >
          <div class="flex items-center truncate">
            <span class="mr-2 font-bold">{{ idx+1 }}.</span>
            <span class="truncate">{{ it.title || 'Item title' }}</span>
          </div>
        </li>
      </ul>
    </nav>

    <!-- 新增項目按鈕 -->
    <div class="px-6 mb-6">
      <button class="block w-full bg-mint-light text-black py-3 rounded-[10px] border-none font-regular cursor-pointer text-center hover:bg-mint-dark transition-colors duration-200" @click="onAdd">
        Add Item
      </button>
    </div>

    <!-- 底部預覽圖 -->
    <div class="mt-auto px-6">
      <div class="w-full" v-if="items[activeIndex] && items[activeIndex].image">
        <img :src="items[activeIndex].image" class="w-full h-[68px] object-cover rounded-[10px] block" />
      </div>
    </div>
  </div>
</template>

