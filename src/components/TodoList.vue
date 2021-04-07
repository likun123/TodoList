<template>
  <div>
    <header>
      <section class="center header-bg">
        <h1>ToDoList</h1>
        <input
          placeholder="添加Todos"
          class="addItem"
          @keyup.enter="addItem"
          type="text"
          v-model="currentValue"
        />
      </section>
    </header>
    <div class="tool-tips text-align-left">
      <div class="check-all">
        <input
          @change="selectAll($event)"
          type="checkbox"
          name="checkall"
          id="checkall"
        />
        <label for="checkall">全选</label>
      </div>
      <div
        class="remove-all"
        @click="deleteChecked"
      >删除勾选</div>
    </div>
    <section class="center">
      正在进行 <span class="fr nums">{{ notFinishedNum }}</span>
    </section>
    <ul>
      <todo-item
        v-for="item in notFinishedItem"
        :key="item.id"
        :item="item"
        draggable="true"
        @dragstart="dragStart(item)"
        @dragend="dragEnd(item)"
        @dragenter.prevent="dragEnter(item)"
        @dragover.prevent="dragOver"
        @keyup.enter="updateItem(item)"
        @removeItem="removeItem(item)"
      />
    </ul>
    <section class="center">
      已经完成 <span class="fr nums">{{ finishedNum }}</span>
    </section>
    <ul class="finished">
      <todo-item
        v-for="item in finishedItem"
        :key="item.id"
        :item="item"
        draggable="true"
        @dragstart="dragStart(item)"
        @dragend="dragEnd(item)"
        @dragenter.prevent="dragEnter(item)"
        @dragover.prevent="dragOver"
        @keyup.enter="updateItem(item)"
        @removeItem="removeItem(item)"
      />
    </ul>
  </div>
</template>
<script>
import TodoItem from "./TodoItem.vue";
export default {
  name: "todoList",
  components: {
    TodoItem,
  },
  data() {
    return {
      currentValue: "",
      dragIndex: 0,
      todoList: [
        {
          id: 1,
          text: "a",
          isFinished: false,
        },
        {
          id: 4,
          text: "q",
          isFinished: false,
        },
        {
          id: 5,
          text: "qqqq",
          isFinished: false,
        },
        {
          id: 2,
          text: "b",
          isFinished: true,
        },
        {
          id: 3,
          text: "c",
          isFinished: true,
        },
      ],
    };
  },
  methods: {
    addItem() {
      this.todoList.push({
        id: Math.max(...this.todoList.map((x) => x.id)) + 1,
        text: this.currentValue,
        isFinished: false,
      });
      this.currentValue = "";
    },
    removeItem(todo) {
      let index = this.todoList.findIndex((item) => item.id === todo.id);
      this.todoList.splice(index, 1);
    },
    selectAll(e) {
      e.target.checked
        ? this.todoList.map((item) => {
            item.isFinished = true;
          })
        : this.todoList.map((item) => {
            item.isFinished = false;
          });
    },
    deleteChecked() {
      this.todoList = this.todoList.filter((item) => {
        return item.isFinished !== true;
      });
    },
    updateItem(item) {
      this.todoList.filter((todo) => {
        if (todo.id === item.id) {
          todo.text = item.text;
        }
      });
      console.log(this.todoList);
    },
    dragStart(item) {
      // 存储移动元素
      this.moveItem = item;
    },
    dragEnd(item) {
      /* 拷贝源数组 */
      let copyArr = this.todoList;
      /* 拷贝原数组的拷贝 */
      let newItems = [...copyArr];
      if (this.moveItem !== this.insertItem) {
        let moveIndex = copyArr.indexOf(this.moveItem);
        let insertIndex = copyArr.indexOf(this.insertItem);
        // 删除老的节点
        newItems.splice(moveIndex, 1);
        // 在列表中目标位置增加新的节点
        newItems.splice(insertIndex, 0, this.moveItem);
      }
      /* 赋值给todolist */
      this.todoList = newItems;
    },
    dragOver() {
      // 防止移动的时候，鼠标变为禁止图标
    },
    dragEnter(item) {
      this.insertItem = item;
    },
  },
  computed: {
    notFinishedNum: function () {
      return this.todoList.filter((item) => {
        return item.isFinished === false;
      }).length;
    },
    finishedNum: function () {
      return this.todoList.filter((item) => {
        return item.isFinished === true;
      }).length;
    },
    finishedItem: function () {
      return this.todoList.filter((item) => {
        return item.isFinished === true;
      });
    },
    notFinishedItem: function () {
      return this.todoList.filter((item) => {
        return item.isFinished === false;
      });
    },
  },
  beforeMount() {
    if (localStorage.getItem("todoList")) {
      this.todoList = JSON.parse(localStorage.getItem("todoList"));
    }
  },
  watch: {
    todoList: {
      // 深度监听
      handler(val, oldVal) {
        localStorage.setItem("todoList", JSON.stringify(val));
      },
      deep: true,
    },
  },
};
</script>
<style>
.text-align-left {
  text-align: left;
}
input {
  border: none;
}
input[type="text"],
input[type="checkbox"],
button {
  height: 22px;
  display: inline-block;
  vertical-align: middle;
}

input[type="checkbox"],
button,
label {
  width: 22px;
  height: 22px;
}
input[type="text"] {
  padding: 5px;
  margin: 5px 10px;
  width: 545px;
}
body {
  background-color: #cdcdcd;
}
header {
  background-color: rgba(47, 47, 47, 0.98);
  padding: 10px 0;
}
header input {
  width: 364px !important;
}
.header-bg {
  display: flex;
  justify-content: space-between;
}
.active {
  text-decoration: line-through;
}
ul{
  width: 620px;
  margin: 0 auto;
  list-style-type: none;
}
h1,
.addItem {
  display: inline-block;
  vertical-align: middle;
}
h1 {
  color: #fff;
}
.addItem {
  border: 1px solid #ddd;
}
.finished li {
  background-color: rgva(221, 221, 221, 0.7);
  cursor: move;
}
.finished li input {
  background-color: transparent;
}
.center {
  width: 620px;
  margin: 0 auto;
  display: flex;
  justify-content: space-between;
}
.fr {
  float: right;
}
.tool-tips {
  width: 620px;
  margin: 10px auto;
}
.remove-all,
.check-all {
  margin-right: 10px;
}
.remove-all {
  display: inline-block;
  padding: 5px 10px;
  border-radius: 5px;
  border: 1px solid #ddd;
  background-color: #fff;
}
.check-all {
  display: inline-block;
}
.remove-all:hover {
  background-color: turquoise;
  color: #fff;
  cursor: pointer;
}
.nums {
  width: 20px;
  height: 20px;
  line-height: 20px;
  background-color: #fff;
  color: #000;
  border-radius: 50%;
  text-align: center;
}
</style>
