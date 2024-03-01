<script setup>
import { ref } from "vue";
const newTodo = ref("");
const todos = ref([
  {
    id: "123",
    title: "範例",
    completed: false,
    editing: false,
    editedTitle: "",
    deleted: false,
  },
]);

// 新增代辦事項
const addTodo = () => {
  //trim刪除多餘空白
  var titleValue = newTodo.value.trim();
  //取得當前時間訂轉成整數
  var timeStamp = Math.floor(Date.now());
  //沒有值就不會觸發
  if (!titleValue) {
    return;
  }
  todos.value.push({
    id: timeStamp,
    title: titleValue,
    completed: false,
    editing: false,
    editedTitle: "",
    deleted: false,
  });
  newTodo.value = ""; //清除內容
};

// 清除所有代辦
const clearAll = () => {
  todos.value.forEach((item) => (item.deleted = true));
};

//還原所有代辦
const restoreAll = () => {
  todos.value.forEach((item) => (item.deleted = false));
};

//編輯代辦
const editTodo = (item) => {
  item.editing = true;
  item.editedTitle = item.title;
};
const saveEdit = (item) => {
  item.title = item.editedTitle;
  item.editing = false;
};

// 刪除單個代辦
const removeTodo = (item) => {
  // const index = todos.value.indexOf(item);
  // //目標存在
  // if (index !== -1) {
  //   todos.value.splice(index, 1);
  // }
  item.deleted = true;
};
//恢復刪除的代辦事項
const restoreTodo = (item) => {
  item.deleted = false;
};

//依tab篩選資料
const visibility = ref("all");
const filteredTodos = () => {
  if (visibility.value == "all") {
    // return todos.value;
    return todos.value.filter((item) => !item.deleted);
  } else if (visibility.value == "active") {
    // var newTodos = [];
    // todos.value.forEach(function (item) {
    //   if (!item.completed) {
    //     newTodos.push(item);
    //   }
    // });
    // return newTodos;
    return todos.value.filter((item) => !item.completed && !item.deleted);
  } else if (visibility.value == "completed") {
    // var newTodos = [];
    // todos.value.forEach(function (item) {
    //   if (item.completed) {
    //     newTodos.push(item);
    //   }
    // });
    // return newTodos;
    return todos.value.filter((item) => item.completed && !item.deleted);
  } else if ((visibility.value = "deleted")) {
    return todos.value.filter((item) => item.deleted);
  }
};
const countUndone = () => {
  var count = 0;
  todos.value.forEach(function (item) {
    item.completed || (!item.completed && item.deleted)
      ? (count += 0)
      : (count += 1);
  });
  return count;
};
</script>

<template>
  <h1 class="font-bold">To do list with Vue3</h1>
  <h2 class="font-bold text-sm text-center mt-4 mb-20">
    可以做新增、修改、刪除（全部或單個）、還原（全部或單個）的功能。<br />
    並切依照狀態篩選並顯示，計算總共未完成的筆數（不包含已刪除但未完成的）。
  </h2>
  <!-- 新增代辦 -->
  <div class="mb-14">
    <input
      v-model="newTodo"
      @keyup.enter="addTodo"
      class="border rounded px-3 py-3 w-2/3 mr-9"
      type="text"
      placeholder="請輸入代辦事項..."
    />
    <button class="bg-black text-white" @click="addTodo" type="button">
      新增
    </button>
  </div>
  <main>
    <!-- tab -->
    <nav class="mb-5">
      <ul class="flex justify-center">
        <li
          class="px-5 py-3 mx-3 cursor-pointer rounded"
          :class="{ 'bg-black text-white': visibility == 'all' }"
          @click="visibility = 'all'"
        >
          全部
        </li>
        <li
          class="px-5 py-3 mx-3 cursor-pointer rounded"
          :class="{ 'bg-black text-white': visibility == 'active' }"
          @click="visibility = 'active'"
        >
          進行中
        </li>
        <li
          class="px-5 py-3 mx-3 cursor-pointer rounded"
          :class="{ 'bg-black text-white': visibility == 'completed' }"
          @click="visibility = 'completed'"
        >
          已完成
        </li>
        <li
          class="px-5 py-3 mx-3 cursor-pointer rounded"
          :class="{ 'bg-black text-white': visibility == 'deleted' }"
          @click="visibility = 'deleted'"
        >
          已刪除
        </li>
      </ul>
    </nav>

    <!-- 內容 -->
    <div
      class="flex justify-between items-center py-5 border-b"
      v-for="(item, key) in filteredTodos()"
    >
      <div class="text-xl align-middle font-bold">
        <label
          v-if="!item.editing"
          :class="{
            'line-through': item.deleted,
          }"
        >
          <input
            class="mr-4 cursor-pointer w-5 h-5 align-middle"
            type="checkbox"
            v-model="item.completed"
            :class="{ hidden: item.deleted }"
          />
          {{ item.title }}
        </label>
        <input
          placeholder="請輸入代辦事項..."
          v-model="item.editedTitle"
          v-if="item.editing"
          @keyup.enter="saveEdit(item)"
          class="border-2 border-blue-400 px-4 py-2 rounded"
        />
      </div>

      <div>
        <span @click="editTodo(item)" v-show="!item.deleted">
          <svg
            class="inline cursor-pointer px-1 hover:fill-black"
            fill="gray"
            height="25px"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 24 24"
          >
            <title>square-edit-outline</title>
            <path
              d="M5,3C3.89,3 3,3.89 3,5V19A2,2 0 0,0 5,21H19A2,2 0 0,0 21,19V12H19V19H5V5H12V3H5M17.78,4C17.61,4 17.43,4.07 17.3,4.2L16.08,5.41L18.58,7.91L19.8,6.7C20.06,6.44 20.06,6 19.8,5.75L18.25,4.2C18.12,4.07 17.95,4 17.78,4M15.37,6.12L8,13.5V16H10.5L17.87,8.62L15.37,6.12Z"
            />
          </svg>
        </span>
        <span @click="removeTodo(item)" :class="{ hidden: item.deleted }">
          <svg
            class="inline cursor-pointer px-1 hover:fill-black"
            fill="gray"
            height="25px"
            xmlns="http://www.w3.org/2000/svg"
            viewBox="0 0 24 24"
          >
            <title>delete-forever</title>
            <path
              d="M6,19A2,2 0 0,0 8,21H16A2,2 0 0,0 18,19V7H6V19M8.46,11.88L9.87,10.47L12,12.59L14.12,10.47L15.53,11.88L13.41,14L15.53,16.12L14.12,17.53L12,15.41L9.88,17.53L8.47,16.12L10.59,14L8.46,11.88M15.5,4L14.5,3H9.5L8.5,4H5V6H19V4H15.5Z"
            />
          </svg>
        </span>
        <span v-show="item.deleted" @click="restoreTodo(item)">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="inline cursor-pointer px-1 hover:fill-black"
            fill="gray"
            height="25px"
            viewBox="0 0 24 24"
          >
            <title>restore</title>
            <path
              d="M13,3A9,9 0 0,0 4,12H1L4.89,15.89L4.96,16.03L9,12H6A7,7 0 0,1 13,5A7,7 0 0,1 20,12A7,7 0 0,1 13,19C11.07,19 9.32,18.21 8.06,16.94L6.64,18.36C8.27,20 10.5,21 13,21A9,9 0 0,0 22,12A9,9 0 0,0 13,3Z"
            />
          </svg>
        </span>
      </div>
    </div>
    <div class="pt-3 flex justify-between items-center">
      <p>
        還有<span class="text-blue-500 px-2 font-semibold">{{
          countUndone()
        }}</span
        >筆事項未完成
      </p>
      <p
        v-show="visibility !== 'deleted'"
        class="text-end cursor-pointer font-bold text-gray-500 hover:text-black"
        @click="clearAll"
      >
        清除所有事項
      </p>
      <p
        v-show="visibility == 'deleted'"
        class="text-end cursor-pointer font-bold text-gray-500 hover:text-black"
        @click="restoreAll"
      >
        還原所有事項
      </p>
    </div>
  </main>
</template>

<style scoped></style>
