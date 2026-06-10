<script setup>
import { reactive, ref, watch } from 'vue'
// 引入側邊欄與編輯頁面組件
import Sidebar from './components/Sidebar.vue'
import Editor from './components/Editor.vue'

// 引入圖片資源
import Image from './assets/image.png'
import deleteIcon from './assets/delete.png'

// 載入已儲存的資料，若無資料則使用預設清單
const savedItems = localStorage.getItem('todo-items')
const defaultItems = [
  { title: 'Item title', startDate: '2022/05/17', endDate: '2022/05/18', image: Image, content: 'content...' }
]
const items = reactive(savedItems ? JSON.parse(savedItems) : defaultItems)

// 當清單內容發生變動時，自動將最新資料存入 localStorage
watch(items, (newVal) => {
  localStorage.setItem('todo-items', JSON.stringify(newVal))
}, { deep: true })

const activeIndex = ref(0) // 當前項目的索引值
const isMobileMenuOpen = ref(false) // 手機版開啟狀態

// 新增一個待辦事項項目
const addItem = () => {
  items.push({ title: 'new item title', startDate:'', endDate:'', image:'', content:'' })
  activeIndex.value = items.length - 1
  isMobileMenuOpen.value = false
}

// 選擇指定的待辦事項
const selectItem = (i) => {
  activeIndex.value = i
  isMobileMenuOpen.value = false
}

// 刪除指定的待辦事項
const deleteItem = (i) => {
  items.splice(i, 1)
  // 若刪除後 activeIndex 超出範圍，則自動調整選取目標至最後一筆
  if(activeIndex.value >= items.length) activeIndex.value = Math.max(0, items.length-1)
  isMobileMenuOpen.value = false
}

// 刪除目前正被選取的項目
const deleteActive = () => {
  if(items.length===0) return
  deleteItem(activeIndex.value)
}

// 更新目前選取項目的內容資料
const updateActive = (payload) => {
  if(items[activeIndex.value]){
    items[activeIndex.value] = { ...payload }
  }
}

// 關閉手機版選單
const closeMobileMenu = () => {
  isMobileMenuOpen.value = false
}
</script>

<template>
  <div class="app-shell">
    <!-- 手機版 header，包含選單按鈕與刪除按鈕 -->
    <div class="mobile-header">
      <button class="menu-btn" @click="isMobileMenuOpen = true" title="Open menu">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M3 12H21" stroke="black" stroke-width="2.5" stroke-linecap="round"/>
          <path d="M3 6H21" stroke="black" stroke-width="2.5" stroke-linecap="round"/>
          <path d="M3 18H21" stroke="black" stroke-width="2.5" stroke-linecap="round"/>
        </svg>
      </button>
      <button class="trash-btn" @click="deleteActive" title="Delete active" v-if="items.length > 0">
        <img :src="deleteIcon" class="w-5 h-5 block" alt="Delete" />
      </button>
    </div>

    <!-- 點擊背景可關閉選單 -->
    <div class="mobile-backdrop" v-if="isMobileMenuOpen" @click="closeMobileMenu"></div>

    <!-- 顯示清單、Add Item 按鈕以及底部預覽圖 -->
    <Sidebar 
      :items="items" 
      :activeIndex="activeIndex" 
      :class="{ open: isMobileMenuOpen }" 
      @add="addItem" 
      @select="selectItem" 
      @delete="deleteItem"
      @close="closeMobileMenu"
    />
    
    <!-- 右側顯示選取項目的細節編輯頁面 -->
    <Editor :item="items[activeIndex]" @update="updateActive" @delete="deleteActive" />
  </div>
</template>

<style src="./style.scss"></style>

