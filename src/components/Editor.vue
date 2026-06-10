<script setup>
import { computed, ref, watch } from 'vue'
import deleteIcon from '../assets/delete.png'  // 垃圾桶icon

const props = defineProps({ item: Object })
const emit = defineEmits(['update','delete'])

// 本地暫存的編輯表單狀態，防雙向綁定直接修改父元件原始資料
const local = ref(props.item ? JSON.parse(JSON.stringify(props.item)) : {
  title: '', startDate: '', endDate: '', image: '', content: ''
})

// 當選取的待辦事項項目改變時，同步至本地暫存狀態
watch(()=>props.item, (v)=>{
  local.value = v ? JSON.parse(JSON.stringify(v)) : { title:'', startDate:'', endDate:'', image:'', content:'' }
})

// 發送更新後的完整項目內容給父元件
const emitField = (field, value) => {
  local.value[field] = value
  emit('update', { ...local.value })
}

// 綁定，用於上傳圖片
const fileInput = ref(null)
// file input 的點擊事件
const triggerFile = () => {
  if(fileInput.value) fileInput.value.click()
}

// 當使用者選取本機圖片檔案後觸發此事件，使用 FileReader 讀取為 Base64 字串並存入項目中
const onFile = (e) => {
  const f = e.target.files && e.target.files[0]
  if(!f) return
  const reader = new FileReader()
  reader.onload = ()=>{
    emitField('image', reader.result)
  }
  reader.readAsDataURL(f)
}

// 發送 'delete' 事件告知父元件
const onDelete = () => { emit('delete') }

// 計算內容的字元長度
const charCount = computed(()=> local.value.content ? local.value.content.length : 0)

// 預設無圖片時顯示的 SVG 預設圖
const placeholderImage = 'data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="600" height="220"><rect width="100%" height="100%" fill="%23EBEBEB" rx="10"/><text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" fill="%23999" font-size="18">No image</text></svg>'
</script>

<template>
  <div class="content-wrap h-full">
    <!-- PC版刪除按鈕 -->
    <div class="flex justify-end mb-4 max-[720px]:hidden" v-if="item">
      <button class="p-1 hover:opacity-70 transition-opacity duration-200 bg-transparent border-none cursor-pointer text-black flex items-center justify-center" @click="onDelete" title="Delete Active">
        <img :src="deleteIcon" class="w-5 h-5 block" alt="Delete" />
      </button>
    </div>

    <div class="relative" v-if="item">
      <!-- 標題與日期輸入欄位 -->
      <div class="flex gap-6 mb-5 max-[720px]:flex-col max-[720px]:gap-5">
        <div class="flex flex-col gap-2 flex-[3]">
          <label class="font-regular text-black">Title</label>
          <input class="w-full px-4 py-3 rounded-[10px] border-none bg-muted outline-none text-black" type="text" :value="local.title" @input="e=>emitField('title', e.target.value)" />
        </div>
 
        <div class="flex flex-col gap-2 flex-[2]">
          <label class="font-regular text-black">Date</label>
          <div class="flex items-center gap-2 w-full">
            <input class="w-full px-4 py-3 rounded-[10px] border-none bg-muted outline-none text-center font-medium text-black" type="text" :value="local.startDate" @input="e=>emitField('startDate', e.target.value)" placeholder="YYYY/MM/DD" />
            <span class="text-black font-medium">~</span>
            <input class="w-full px-4 py-3 rounded-[10px] border-none bg-muted outline-none text-center font-medium text-black" type="text" :value="local.endDate" @input="e=>emitField('endDate', e.target.value)" placeholder="YYYY/MM/DD" />
          </div>
        </div>
      </div>
 
      <!-- 圖片上傳與網址輸入欄位 -->
      <div class="flex flex-col gap-2 mb-5">
        <label class="font-regular text-black">Image</label>
        <div class="flex gap-6 w-full max-[720px]:flex-col max-[720px]:gap-3">
          <div class="flex-1 flex flex-col justify-between gap-2">
            <button class="w-full bg-mint-light text-black py-2 rounded-[10px] border-none font-regular cursor-pointer text-center hover:bg-mint-dark transition-colors duration-200" @click.prevent="triggerFile">
              Upload Image
            </button>
            <input ref="fileInput" type="file" accept="image/*" @change="onFile" class="hidden" />
            <div class="text-center text-black/65 text-sm font-medium my-0.5">or</div>
            <input class="w-full px-4 py-2 rounded-[10px] border-none bg-muted outline-none text-black" placeholder="請輸入圖片網址" :value="local.image" @input="e=>emitField('image', e.target.value)" />
          </div>
          <div class="flex-1 h-[120px] flex items-center justify-center max-[720px]:h-[140px]">
            <img :src="local.image || placeholderImage" class="w-full h-full object-cover rounded-[10px]" />
          </div>
        </div>
      </div>
 
      <!-- 內容欄位 -->
      <div class="flex flex-col gap-2 mb-0">
        <label class="font-regular text-[15px] text-black">Content</label>
        <div class="relative">
          <textarea class="w-full p-4 rounded-[10px] bg-muted border-none outline-none resize-none text-[15px] text-black" rows="6" :value="local.content" @input="e=>emitField('content', e.target.value.slice(0, 200))" placeholder="content..." maxlength="200"></textarea>
          <div class="custom-char-badge">200 / {{ charCount }}</div>
        </div>
      </div>
    </div>
    
    <!-- 無項目時 -->
    <div class="flex items-center justify-center h-full text-black/45 font-medium max-[720px]:py-10" v-else>
      <p>請先新增或選擇一個項目</p>
    </div>
  </div>
</template>
