<script setup>
import { v4 as uuidv4 } from 'uuid';
import { onMounted, ref, watch } from 'vue';

const notes = ref([{
  id: uuidv4(),
  content: '',
  show: true,
}]);
const text = ref('');

function addNote() {
  const note = {
    id: uuidv4(),
    content: '',
    show: true,  
  };
  notes.value.push(note);
  notes.value[notes.value.length - 2].show = false;
  text.value = '';
}

function removeNote(index) {
  let targetIndex = notes.value.findIndex(note => note.id == index);
  let prev = notes.value[notes.value.length - 2];        
  if (prev) {
    notes.value.splice(targetIndex, 1);
    let last = notes.value[notes.value.length - 1];
    text.value = last.content;
    last.show = true;
  } else {
    text.value = '';
    notes.value[0].show = true;
    notes.value[0].content = '';
  }
}

function showNote(index) {
  for(let note of notes.value) {
    note.show = false;
  }
  let target = notes.value.find(note => note.id === index);
  target.show = true;
  text.value = target.content;
}

function enterIn() {
  let target = notes.value.find(note => note.show === true);
  target.content = text.value;        
}

watch(
  notes,
  value => {
    localStorage.setItem('notesData', JSON.stringify(value));
  },
  { deep: true }
)

onMounted(() => {
  if (localStorage.getItem('notesData')) {
    notes.value = JSON.parse(localStorage.getItem('notesData'));
    text.value = notes.value[notes.value.length - 1].content;
  }
})
</script>

<template>
  <div class="container">
    <h1>Online Notes</h1>
    <div class="notes-container">      
      <main>
        <button class="add" @click="addNote">+ New Note</button>
        <textarea placeholder="type your notes here..." v-model="text" @input="enterIn"></textarea>
      </main>
      <div class="notes-list">
        <ul>
          <li v-for="note of notes" :key="note.id" @click="showNote(note.id)" v-cloak>
            <p>{{ note.content }}</p>
            <button class="remove" @click.stop="removeNote(note.id)">&#x2716;</button>
          </li>
        </ul>
      </div>
    </div>    
  </div>
</template>

<style scoped>
.container {
  max-width: 1000px;
  margin: 0 auto;
}

h1 {
  display: block;
  margin: 40px 0;
  font-weight: normal;
  font-size: 48px;
  text-align: center;
  color: var(--primary);
}

.notes-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
}

main {
  position: relative;
  width: 100%;
  height: 90%;
}

.add {
  position: absolute;    
  top: 0; 
  right: 0;
  transform: translateY(-150%);
  display: inline-block;
  margin-left: auto;
  background-color: var(--Secondary);
  border: none;
  cursor: pointer;
  padding: 10px;
  border-radius: 5px;
  color: #fff;
  font-size: 18px;
  font-weight: bold;    
}

.add:hover {
  background-color: var(--hover);
}

textarea {
  display: block;
  padding: 20px;
  width: 100%;
  height: 250px;
  resize: none;
  border-radius: 10px;
  border: 0;
  outline: none;
  font-size: 24px;
  margin: 0 auto;
}

textarea::-webkit-scrollbar {
  width: 10px;
}

textarea::-webkit-scrollbar-button {
  background: transparent;
  border-radius: 4px;
}

textarea::-webkit-scrollbar-track-piece {
  background: transparent;
}

textarea::-webkit-scrollbar-thumb {
  border-radius: 4px;
  background-color: rgba(0, 0, 0, 0.4);
  border: 1px solid slategrey;
}

textarea::-webkit-scrollbar-track {
  box-shadow: transparent;
}

.notes-list {
  width: 100%;
  overflow-x: auto;
  overflow-y: hidden;
  margin: 50px 0;
}

.notes-list::-webkit-scrollbar {
  height: 10px;
}

.notes-list::-webkit-scrollbar-button {
  background: transparent;
  border-radius: 4px;
}

.notes-list::-webkit-scrollbar-track-piece {
  background: transparent;
}

.notes-list::-webkit-scrollbar-thumb {
  border-radius: 4px;
  background-color: rgba(0, 0, 0, 0.4);
  border: 1px solid slategrey;
}

.notes-list::-webkit-scrollbar-track {
  box-shadow: transparent;
}

ul {
  display: flex;
  width: 100%;
  align-items: center;
  margin-bottom: 8px;
}

li {
  flex-shrink: 0;
  position: relative;
  border-bottom: 1px solid #c1bebc;
  font-size: 18px;
  width: 170px;
  cursor: pointer;
}

li + li {
  margin-left: 20px;
}

p {
  overflow : hidden;
  text-overflow : ellipsis;
  white-space : nowrap;
  width : 150px;
  height: 22px;
}

  p:hover {
  color: var(--Secondary)
}

.remove {
  position: absolute;
  right: 0;
  top: -7px;
  cursor: pointer;
  background-color: transparent;
  border: none;
  color: var(--remove);
  font-size: 24px;
}

.remove:hover {
  color: var(--primary);
}
</style>