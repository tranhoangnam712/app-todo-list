<script setup>
import { ref ,computed,nextTick} from 'vue'
const draft = ref("")
const tasks = ref([{id:1,name:"Make Khanh happy",done:false},])
const id = ref(2)
const current_filter = ref("All")
const editId= ref(null)
const editdraft= ref("")
const remain = computed(()=>
  tasks.value.reduce((count,t)=>{
    if (!t.done) return ++count
    else return count
    }
  ,0)
)
const filtered_tasks = computed(()=>{
  if (current_filter.value !=="All"){
    return tasks.value.filter((t)=>current_filter.value==="Active"?!t.done:t.done)
  }
  return tasks.value
})
const addToList=()=>{
  if (draft.value===""){
    return 
  }
  tasks.value.push({id:id.value,name:draft.value,done:false})
  id.value++
  draft.value = ""
}
const deleteItem=(item)=>{
  tasks.value= tasks.value.filter((t)=>
    t.id!==item.id
)

}
const startEdit= async (item)=>{
    editId.value= item.id
    editdraft.value = item.name
    await nextTick()
    const inp= document.getElementById('editinp'+item.id)
    inp.focus()
}
const saveEdit=(item)=>{
  item.name = editdraft.value===""?item.name:editdraft.value
  editId.value=null
  editdraft.value = ""
}
const clearComplete=()=>{
  tasks.value = tasks.value.filter((t)=>!t.done)
}
</script>

<template>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document Title</title>
    <!-- Link your external CSS file here -->
    <link rel="stylesheet" href="style.css">
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:ital,wght@0,100..900;1,100..900&display=swap" rel="stylesheet">

</head>
<body>
    <main id="main">
        <header class="hero">
            <h1 class="hero__title">
              TodoList - Chapter 5
            </h1>
            <form @submit.prevent="addToList()" class="hero__form">
              <input v-model.trim="draft" class="hero__form" @keydown.enter.prevent="addToList()" placeholder="Add task to do">
              <button type="submit">Add</button>
            </form>
        </header>
        <div class="filter">
          <span class="filter__item" :class="{active:current_filter==='All'}" @click="current_filter='All'">
            All
          </span>
          <span class="filter__item" :class="{active:current_filter==='Active'}" @click="current_filter='Active'">
            Active
          </span>
          <span class="filter__item" :class="{active:current_filter==='Completed'}" @click="current_filter='Completed'">
            Completed
          </span>
        </div>
        <ul>
            <li v-for="item in filtered_tasks" :key="item.id">
              <input :id="'chkbox'+item.id" type="checkbox" v-model="item.done">
              <label v-if="editId!==item.id" @dblclick="startEdit(item)" :for="'chkbox'+item.id" :style="{'text-decoration':item.done?'line-through':'none'}">{{item.name}}</label>
              <input :id="'editinp'+item.id" v-else v-model.trim="editdraft" @keyup.enter="saveEdit(item)" @keyup.esc="editId=null;editdraft=''" @blur="saveEdit(item)"> 
              <button @click="deleteItem(item)">X</button>
            </li>
        </ul>
        <footer class="footer">
          <div class="footer__remain">
              {{remain}} task left
          </div>
          <button @click="clearComplete">
            Clear completed
          </button>
        </footer>
    </main>
</body>
</html>
</template>
