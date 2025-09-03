<script setup>
import todoApp from './components/todoApp.vue';
import addList from './components/addList.vue';
import {onMounted,ref} from 'vue';
const notes = ref([]);

const noteId = ref(1);
onMounted(() => {
  const storedNotes = localStorage.getItem('notes');
  if (storedNotes) {
    notes.value = JSON.parse(storedNotes);//改回物件
    if (notes.value.length > 0) {
      const maxId = Math.max(...notes.value.map(note => note.id));
      noteId.value = maxId + 1;// 獲取最大ID並加1是下一個新增的id
    }
  }
});
function addNote(note) {
  notes.value.push({
    id: noteId.value++,
    title: note.title,
    color: note.color
  });
  saveNotes();
}
function saveNotes() {
  localStorage.setItem('notes', JSON.stringify(notes.value));
}
function deleteNote(id) {
  notes.value = notes.value.filter(note => note.id !== id);
  saveNotes();
  // 同時清理該便利貼的待辦事項
  localStorage.removeItem(`todos-${id}`);
  localStorage.removeItem(`date-${id}`);
}


</script>

<template>
  <main>
    <div id="notes">
      <todoApp
        v-for="note in notes"
        :key="note.id"
        :id="note.id"   
        :color="note.color"
        :title="note.title"
        @delete="deleteNote"  
      />
    </div>
  
  </main>
  <addList @addlist="addNote" />
  
 

</template>
<style scoped>
template{

  height: 100vh;
  width: 100vw;
  background-color: #e1e1e1d6;
}
#notes{
  display: flex;
    flex-wrap: wrap;
    justify-content: center;
    gap: 25px;
    margin: 20px;
    /* background-color: #f7bbc0; */
    /* z-index: -1; */
    padding: 20px;
  
    width: 100%;
    height: 100%;

}
main{
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  /* background-color: #0d474d; */
  position: relative;
}

</style>
