<template>
   <div id="app" class="container my-3">
    <div class="input-group mb-3">
      <span class="input-group-text">待辦事項</span>
      <input type="text" class="form-control" placeholder="準備要做的任務"
        id="newTodo" v-model="newTodo" @keyup.enter="addItem">
      <button class="btn btn-primary" type="button" id="addTodo" @click="addItem">新增</button>
    </div>
    <div class="card text-center">
      <ul class="list-group list-group-flush text-left" id="todoList">
        <li  class="list-group-item" v-for="item in filterData" :key="item.id">
          <div class="d-flex">
            <div class="form-check">
              <input
                class="form-control"
                type="text"
                v-model="tempTitle"
                @keyup.enter="done"
                 v-if="tempId === item.id"
              />
              <div v-else>
                <input
                  :id="item.id"
                  type="checkbox"
                  class="form-check-input"
                  v-model="item.checked"
                  @change="setLocalStorage"
                >
                <label
                  :for="item.id"
                  :class="{ 'completed': item.checked }"
                  @dblclick="editItem(item)"
                >
                  {{ item.title }}
                </label>
              </div>
            </div>
            <button type="button" class="btn-close ms-auto remove" @click="removeItem(item)">
            </button>
          </div>
        </li>
      </ul>
      <div class="card-footer d-flex justify-content-between">
        <span>有 <span id="taskCount">{{ filterData.length }}</span> 筆任務</span>
        <div>
          <a href="#" class="me-3" @click.prevent="filterStatus = 'all'">全部</a>
          <a href="#" class="me-3" @click.prevent="filterStatus = 'active'">未完成</a>
          <a href="#" class="me-3" @click.prevent="filterStatus = 'completed'">已完成</a>
          <a href="#" @click="removeAll">清除所有任務</a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed } from 'vue'

export default {
  name: 'Home',
  setup() {
    const newTodo = ref('');
    const todoList = ref(JSON.parse(getLocalStorage())) || ref([]);

    // 新增、刪除
    function addItem() {
      const item = {
        id: Date.now(),
        title: newTodo.value,
        checked: false,
        showInput: false,
      };
      todoList.value.push(item);
      setLocalStorage();
      newTodo.value = '';
    }

    function removeItem(item) {
      const index = todoList.value.findIndex((el) => el.id === item.id);
      todoList.value.splice(index, 1);
      setLocalStorage();
    }

    // 編輯、完成
    const tempTitle = ref('');
    const tempId = ref('');
    function editItem(item) {
      tempTitle.value = item.title;
      tempId.value = item.id;
    }

    function done() {
      todoList.value.forEach((el) => {
        if (el.id === tempId.value) {
          el.title = tempTitle.value;
        }
      });
      setLocalStorage();
      tempId.value = '';
    }

    function removeAll() {
      todoList.value = [];
      setLocalStorage();
    }

    const filterStatus = ref('all');
    const filterData = computed((el) => {
      switch(filterStatus.value) {
        case 'active':
          return todoList.value.filter((el) => el.checked === false);
          break;
        case 'completed':
          return todoList.value.filter((el) => el.checked !== false);
          break;
        default:
          return todoList.value
      }
    })

    function getLocalStorage() {
      return localStorage.getItem('todoList');
    }
    function setLocalStorage() {
      localStorage.setItem('todoList', JSON.stringify(todoList.value));
    }

    return {
      newTodo,
      todoList,
      addItem,
      removeItem,
      tempTitle,
      tempId,
      editItem,
      done,
      removeAll,
      filterStatus,
      filterData,
      getLocalStorage,
      setLocalStorage,
    };
  }
};
</script>
<style>
  .completed {
    text-decoration: line-through
  }
</style>
