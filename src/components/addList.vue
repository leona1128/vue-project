<template>
  <div class="addList">
    <button class='addBtn' type="button" @click="addNote"></button>
    <div class="addList_container" v-if="showAddList">
      <div class="addList_header">
        <input
          v-model="title"
          type="text" 
          placeholder="標題"
        />
        <button @click="closeAddList">❌</button>
      </div>
      
      <div class="color-section"   @click="openColorPicker">
        <label for="color">選擇顏色:</label>
        <div class="color-picker">
      
          <input 
            type="color" 
            v-model="newNoteColor" 
            id="color"
            class="hidden-colorInput"
          />
          <!-- 自定義顏色顯示按鈕 -->
          <div 
            class="custom-color-btn" 
            @click="openColorPicker"
            :style="{ backgroundColor: newNoteColor }"
          >
          </div>
        </div>
      </div>
      
      <button type="button" @click="submitNote" class="submit-btn">新增</button>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const emit = defineEmits(['addlist', 'close'])
const showAddList = ref(false)
const newNoteColor = ref('#ffffff')
const title = ref('')

function addNote() {
  showAddList.value = true
}

function closeAddList() {
  showAddList.value = false
  title.value = ''
  newNoteColor.value = '#ffffff' // 重置顏色
}

function submitNote() {
  console.log('按了新增按鈕！') 
  if (title.value.trim() !== '') {
    console.log('要發送的資料：', { title: title.value, color: newNoteColor.value }) 
    emit('addlist', {
      title: title.value.trim(),
      color: newNoteColor.value
    })
    closeAddList()
  }
}

function openColorPicker() {
  document.getElementById('color').click()
  console.log('開啟顏色選擇')
}
</script>

<style scoped>
.addList {
  position: fixed;
  bottom: 10px;
  left: 40px;
  z-index: 1000;
}

.addBtn {
  font-size: 50px;
  border: none;
  cursor: pointer;
  border-radius: 50%;
  width: 70px;
  height: 70px;
  display: flex;
  align-items: center;
  justify-content: center;
  background-image: url('../assets/img/addlist.png');
  background-size: cover;
  opacity: 0.5;
}

.addBtn:hover {
  opacity: 1;
  transition: opacity 0.5s ease;
  transform: scale(1.1);
}

.addList_container {
  position: absolute;
  bottom: 80px;
  left: 0;
  background-color: #7fc7ffc9;;
  backdrop-filter: blur(10px);
  width: 300px;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
}

.addList_header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.addList_header input {
  flex: 1;
  padding: 8px 12px;
  border: 1px solid #ccc;
  border-radius: 6px;
  margin-right: 10px;
  font-size: 14px;
}

.addList_header button {
  background-color: transparent;
  border: none;
  cursor: pointer;
  font-size: 16px;
  padding: 5px;
}

.color-section {
  margin-bottom: 15px;
    display: flex;
    align-items: center;
    gap: 20px;
    width: 100%;
    cursor: pointer;
  transition: background-color 0.2s ease;
  touch-action: manipulation;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0.1);

}
.color-section:hover {
  background-color: rgba(255, 255, 255, 0.1);
  /* 測試用途 */
}

.color-section label {
  display: block;
  margin-bottom: 8px;
  font-size: 14px;
  color: #333;
  pointer-events: none;

}

.color-picker {
  position: relative;
  display: inline-block;
  pointer-events: none;
}

.hidden-colorInput {
  position: absolute;
  opacity: 0;
  pointer-events: none;
  width: 0;
  height: 0;
}

.custom-color-btn {
  width: 40px;
  height: 40px;
  border: 3px solid #fff;
  border-radius: 50%;
  cursor: pointer;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.2);
  transition: transform 0.2s ease;
}

.custom-color-btn:hover {
  transform: scale(1.1);
  transition: transform 0.2s ease;
}

.submit-btn {
  background-color: #458ad5;
  color: white;
  border: none;
  padding: 8px 16px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
  width: 100%;
  
}

.submit-btn:hover {

  transform: scale(1.1);
  transition: transform 0.3s ease;

}
</style>