<script setup>
import { ref, computed, onMounted } from 'vue'

const emit = defineEmits(['delete'])

const editingIndex = ref(null);
const editingText = ref('');
const props = defineProps({
  color: {
    type: String,
    default: '#fff'
  },
  title: {
    type: String,
    default: '便利貼'
  },
  id: {
    type: Number,
    required: true
  }
});

const isEditingDate = ref(false);
const editingDateText = ref('');
const todos = ref([]);
const today = new Date().toISOString().split('T')[0];
const date = ref(today);
const showInput = ref(false);


const selectedType = ref('todo'); 

const undone = computed(() => {
  
  return todos.value.filter(item => item.type === 'todo' && !item.completed).length;
});

const newTodo = ref('');

onMounted(() => {
  const storedTodos = localStorage.getItem(`todos-${props.id}`);
  if (storedTodos) {
    todos.value = JSON.parse(storedTodos);
  }
  const locaDate = localStorage.getItem(`date-${props.id}`);
  if (locaDate) {
    date.value = JSON.parse(locaDate);
  }
});

const toggleInput = () => {
  showInput.value = !showInput.value;
  // 重置選擇
  selectedType.value = 'todo';
}

const clostInput = () => {
  showInput.value = false;
  newTodo.value = '';
  selectedType.value = 'todo';
}
const addClasses = computed(() => ({
  'add-with-bg': !showInput.value, 
}))


function addTodo() {
  if (newTodo.value.trim() !== '') {
    const newItem = {
      text: newTodo.value.trim(),
      type: selectedType.value, 
      completed: false 
    };
    
    todos.value.push(newItem);
    newTodo.value = '';
    showInput.value = false;
    selectedType.value = 'todo';
    storedTodos();
  }
}

function storedTodos() {
  localStorage.setItem(`todos-${props.id}`, JSON.stringify(todos.value));
}

function deleteTodo(index) {
  todos.value.splice(index, 1);
  storedTodos();
}

function editTodo(index) {
  editingIndex.value = index;
  editingText.value = todos.value[index].text;
}

function saveEdit(index) {
  if (editingText.value.trim() !== '') {
    todos.value[index].text = editingText.value.trim();
    editingIndex.value = null;
    editingText.value = '';
    storedTodos();
  }
}

function editdate() {
  isEditingDate.value = true;
  editingDateText.value = date.value;
}

function saveDate() {
  if (editingDateText.value.trim() !== '') {
    date.value = editingDateText.value.trim();
    isEditingDate.value = false;
    editingDateText.value = '';
    locaDate();
  }
}

function locaDate() {
  localStorage.setItem(`date-${props.id}`, JSON.stringify(date.value));
}

function deleteThisNote() {
  emit('delete', props.id);
}

// 新增：切換項目類型
function selectType(type) {
  selectedType.value = type;
}
</script>

<template>
  <div class="card" :style="{ backgroundColor: props.color }">
    <div class="delete" @click="deleteThisNote">✖</div>
    <div class="text">
      <div class="title">
        <div class="title_name">
          <h1 class="name">{{ props.title }}</h1>
          <h2>
            <template v-if="isEditingDate">
              <input 
                v-model="editingDateText" 
                @keyup.enter="saveDate"
                @blur="saveDate"
                type="date" 
                class="date-input"
              />
            </template>
            <template v-else>
              <span @click="editdate">{{ date }}</span>
            </template>
          </h2>
        </div>
      </div>
      <div class="undone">未完成項目：{{ undone }}</div>

      <!-- 代辦事項和留言列表 -->
      <ul>
        <li v-for="(item, index) in todos" :key="index" @dblclick="editTodo(index)">
          <div class="item-content">
            <template v-if="editingIndex === index">
              ✏️
              <input
                v-model="editingText"
                @keyup.enter="saveEdit(index)" 
                @blur="saveEdit(index)"
                type="text"
                class="edit-input"
              /> 
            </template>
            <template v-else>
              <!-- 根據類型顯示不同內容 -->
              <div v-if="item.type === 'todo'" class="todo-item">
                <input 
                  type="checkbox" 
                  :id="'todo-' + index" 
                  v-model="item.completed" 
                  @change="storedTodos" 
                />
                <label :for="'todo-' + index" :class="{ completed: item.completed }">
                  {{ item.text }}
                </label>
             
              </div>

              <div v-else class="message-item">
    
                <span class="message-icon">💬</span>
                <span class="message-text">{{ item.text }}</span>
              </div>
            </template>
          </div>
          <div @click="deleteTodo(index)">❌</div>
        </li>
      </ul>
    </div>
    
    <div class="newListIcon">
      <div class="add" :class="addClasses" @click="toggleInput"> </div>
      <template v-if="showInput">
        <div class="close" @click="clostInput">X</div>
        <div class="choose">
          <div class="list_todo" @click="selectType('todo')">
            <input 
              type="radio" 
              id="list" 
              :checked="selectedType === 'todo'"
              @change="selectType('todo')"
            />
            <span>待辦事項</span>
          </div>
          <div class="list_message" @click="selectType('message')">
            <input 
              type="radio" 
              id="message" 
              :checked="selectedType === 'message'"
              @change="selectType('message')"
            />
            <span>留言</span>
          </div>
        </div>
        <input 
          type="text" 
          v-model="newTodo" 
          :placeholder="selectedType === 'todo' ? '請輸入待辦事項' : '請輸入留言內容'" 
          @keyup.enter="addTodo" 
        />
        <button id="addTodo" @click="addTodo">新增</button>
      </template>
    </div>
    <div class="card_foldUp"></div>
    <div class="tape"></div>
  </div>
</template>

<style scoped>
.card {
  max-width: 600px;
  max-height: 600px;
  width: 300px;
  height: 300px;
  background-color: #fff;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
  padding: 10px;
  position: relative;
}

.card_foldUp {
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 100%;
  transform: rotate(3deg);
  z-index: -1;
  background-color: #c7c7c78c;
}

.tape {
  position: absolute;
  top: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 60px;
  height: 30px;
  background-color:   #6daff6c2;
  box-shadow: 0px 0px 4px rgb(0 0 0 / 49%);
  z-index: 100;
}

.text {
  position: relative;
  height: 100%;
  width: 100%;
  overflow: auto;
}

.title {
  display: flex;
  align-items: flex-end;
  margin-bottom: 10px;
}

.title_name {
  display: flex;
  align-items: flex-end;
  flex-grow: 1;
}

.name {
  width: 70%;
}

.delete {
  position: absolute;
  right: 5px;
  top: 0px;
  color: #9f3414;
  filter: drop-shadow(1px 1px 5px #d4d4d4);
  cursor: pointer;
}

.undone {
  font-size: 12px;
  color: #585757;
  margin-bottom: 5px;
}

h1 {
  font-size: 20px;
  padding-right: 10px;
}

h2 {
  font-size: 12px;
}

h2 span {
  cursor: pointer;
  padding: 2px 4px;
  border-radius: 3px;
}

h2 span:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

ul {
  padding: 5px;
  list-style: none;
}

li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 5px;
  border-bottom: 1px solid #ccc;
}

.item-content {
  flex: 1;
  display: flex;
  align-items: center;
}

.todo-item {
  display: flex;
  align-items: center;
  width: 100%;
}

.message-item {
  display: flex;
  align-items: center;
  width: 100%;
}

.message-icon {
  margin-right: 8px;
  font-size: 14px;
}

.message-text {
  color: #666;
  max-width: 222px;
  max-height: 30px;
  line-height: 14px;
  display: flex;
  word-break: break-word;
  font-size: clamp(0.75rem, 0.25rem + 4vw, 0.875rem);
  overflow-x: hidden;
  overflow-y: auto;
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE 和 Edge */
}
.message-text::-webkit-scrollbar {
  display: none; /* Chrome, Safari, Opera */
}
.completed {
  text-decoration: line-through;
  color: #999;
}

.edit-input {
  border: none;
  outline: none;
  background: transparent;
  flex: 1;
}

.edit-input:focus {
  outline: none;
  border: none;
}

input[type="checkbox"] {
  margin-right: 5px;
}

label {
  padding-right: 10px;
    cursor: pointer;
    flex: 1;
    max-width: 230px;
    max-height: 30px;
    /* flex-wrap: wrap; */
    display: flex;
    word-break: break-word;
    font-size: clamp(0.875rem, 0.375rem + 4vw, 1rem);
    overflow-x: hidden;
  overflow-y: auto;
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none; /* IE 和 Edge */
}

.newListIcon {
  display: flex;
  align-items: center;
  position: absolute;
  bottom: 3px;
  right: 3px;
  font-size: 24px;
  cursor: pointer;
  gap: 3px;
}

.add {
  padding: 0 5px;
  /* background-color: white;
  border-radius: 50%; */
  /* background-image: url('../assets/img/bgadd.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  width: 20px;
  height: 20px; */

}
.add-with-bg {
  background-image: url('../assets/img/bgadd.png');
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  width: 20px;
  height: 20px;

}

#addTodo {
  background-color: #6daff6;
  color: #fff;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
  margin-top: 5px;
}

.close {
  position: absolute;
  bottom: 50px;
  right: 15px;
    text-align: center;
    cursor: pointer;
    font-size: 13px;
    color: #fff;
    background-color: #434a4a;
    width: 20px;
    height: 20px;
    line-height: 20px;
    /* text-align: center; */
    border-radius: 50%;
    box-shadow: 1px 1px 5px 1px #d4d4d4;
}

.choose {
  position: absolute;
  bottom: 45px;
  left: 0px;
  display: flex;
  gap: 10px;
  background-color: rgba(255, 255, 255, 0.9);
  padding: 8px;
  border-radius: 5px;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.2);
}

.list_todo, .list_message {
  display: flex;
  align-items: center;
  gap: 5px;
  cursor: pointer;
  padding: 5px;
  border-radius: 3px;
  transition: background-color 0.2s;
}

.list_todo:hover, .list_message:hover {
  background-color: rgba(0, 0, 0, 0.1);
}

.list_todo span, .list_message span {
  font-size: 12px;
  user-select: none;
}

.date-input {
  border: 1px solid #ccc;
  border-radius: 3px;
  padding: 1px 3px;
  font-size: 12px;
}
</style>