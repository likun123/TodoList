<template>
  <section class="container">
    <aside class="left-menu">
      <ul>
        <li
          @click="checkMenu(item.id)"
          v-for="item in todoList"
          :key="item.id"
          :class="{ active : todoList.length > 0 ? item.id === currentMenuId : false }"
        >
          {{item.menuname}}

          <span class="fr">
            {{item.data.filter(item=>{
          return item.isFinished === false
        }).length}}
          </span>
        </li>
        <li
          class="add-menu-btn"
          @click="addMenu"
        >+ 新增</li>
      </ul>
    </aside>
    <section class="current-todolist">
      <header>
        <!-- <h1>ToDoList</h1> -->
        <div class="current-todo-info">
          <h2>
            {{currentMenu}}
            <span class="current-todo-num">{{ notFinishedNum }}</span>
            <div class="nav-tools fr">
              <span @click="isLocked">locked</span>
              <button @click="removeMenu">X</button>
            </div>
          </h2>
          <input
            placeholder="添加Todos"
            class="addItem"
            @keyup.enter="addItem"
            type="text"
            v-model="currentValue"
          />

        </div>
      </header>
      <main>
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
            class="remove-all btn"
            @click="deleteChecked"
          >删除勾选</div>
        </div>
        <section class="center">
          正在进行 <span class="fr nums">{{ notFinishedNum }}</span>
        </section>
        <ul class="todo-list-wrap not-finished">
          <todo-item
            v-for="item in notFinishedItem"
            :key="item.id"
            :item="item"
            draggable="true"
            @dragstart="dragstart(item)"
            @dragenter="dragEnter(item)"
            @dragover="dragOver"
            @dragend="dragEnd(item)"
            @removeItem="removeItem(item)"
          />
        </ul>
        <section class="center">
          已经完成 <span class="fr nums">{{ finishedNum }}</span>
        </section>
        <ul class="todo-list-wrap finished">
          <todo-item
            v-for="item in finishedItem"
            :key="item.id"
            :item="item"
            draggable="true"
            @dragstart="dragstart(item)"
            @dragenter="dragEnter(item)"
            @dragover="dragOver"
            @dragend="dragEnd(item)"
            @removeItem="removeItem(item)"
          />
        </ul>
      </main>
    </section>
  </section>
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
      currentTodo: [],
      currentMenu: "",
      currentMenuId: "",
      todoList: [],
    };
  },
  methods: {
    genID(length) {
      return Number(
        Math.random().toString().substr(3, length) + Date.now()
      ).toString(36);
    },
    addItem() {
      if (this.currentValue) {
        let id = this.genID(9);
        // this.currentTodo.length > 0 ? Math.max(...this.currentTodo.map((x) => x.id)) + 1 : 1
        this.currentTodo.push({
          id: id,
          text: this.currentValue,
          isFinished: false,
          isLocked: false,
        });
        this.currentValue = "";
      }
    },
    removeItem(todo) {
      let index = this.currentTodo.findIndex((item) => item.id === todo.id);
      this.currentTodo.splice(index, 1);
    },
    selectAll(e) {
      e.target.checked
        ? this.currentTodo.map((item) => {
            item.isFinished = true;
          })
        : this.currentTodo.map((item) => {
            item.isFinished = false;
          });
    },
    deleteChecked() {
      this.currentTodo = this.currentTodo.filter((item) => {
        return item.isFinished !== true;
      });
    },
    updateItem(item) {
      this.currentTodo.filter((todo) => {
        if (todo.id === item.id) {
          todo.text = item.text;
        }
      });
    },
    dragstart(item) {
      // 存储移动元素
      this.moveItem = item;
    },
    dragEnd() {
      /* 拷贝源数组 */
      let copyArr = this.currentTodo;
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
      this.currentTodo = newItems;
    },
    dragOver() {
      // 防止移动的时候，鼠标变为禁止图标
    },
    dragEnter(item) {
      this.insertItem = item;
    },
    checkMenu(menuId) {
      let checkMenu = this.todoList.filter((item) => {
        return item.id === menuId;
      })[0];

      this.currentTodo = checkMenu.data;
      this.currentMenuId = checkMenu.id;
      this.currentMenu = checkMenu.menuname;
    },
    addMenu() {
      this.todoList.push({
        id: this.genID(9),
        menuname: "newTodoList",
        data: [],
        islocked: false,
      });
      let lastIndex = this.todoList.length - 1;
      // console.log(this.todoList[lastIndex].id);
      this.currentTodo = this.todoList[lastIndex].data;
      this.currentMenuId = this.todoList[lastIndex].id;
      this.currentMenu = this.todoList[lastIndex].menuname;
    },
    removeMenu() {},
    isLocked() {},
  },
  computed: {
    notFinishedNum() {
      return this.currentTodo.filter((item) => {
        return item.isFinished === false;
      }).length;
    },
    finishedNum() {
      return this.currentTodo.filter((item) => {
        return item.isFinished === true;
      }).length;
    },
    finishedItem() {
      return this.currentTodo.filter((item) => {
        return item.isFinished === true;
      });
    },
    notFinishedItem() {
      return this.currentTodo.filter((item) => {
        return item.isFinished === false;
      });
    },
  },
  beforeMount() {
    /* if (localStorage.getItem("todoList")) {
      this.todoList = JSON.parse(localStorage.getItem("todoList"));
    } */
  },
  watch: {
    /* todoList: {
      // 深度监听
      handler(val) {
        localStorage.setItem("todoList", JSON.stringify(val));
      },
      deep: true,
    }, */
  },
};
</script>
<style lang="scss">
body {
  background-color: #315481;
  background-image: linear-gradient(180deg, #315481, #918e82);
  background-repeat: no-repeat;
  background-attachment: fixed;
}
.fr {
  float: right;
}
.container {
  display: flex;
  justify-content: center;
  // align-items: center;
  width: 90%;
  padding: 0.5%;
  margin: 0 auto;
  flex-wrap: wrap;
}
ul {
  list-style-type: none;
}
.current-todo-num {
  background: #2cc5d2;
  border-radius: 1em;
  color: #fff;
  display: inline-block;
  font-size: 0.7rem;
  line-height: 1;
  padding: 0.3em 0.5em;
  vertical-align: middle;
}
.left-menu {
  width: 20%;
  height: 95vh;
  text-align: center;
  overflow: auto;
  li {
    padding: 10px;
    color: #ddd;
    cursor: pointer;
    // background-color: rgba(154,154,154,.37);
    box-shadow: 0 1px 0 0 hsl(0deg 0% 100% / 15%);
    &:hover,
    &.active {
      color: #5db9ff;
    }
    span {
      @extend .current-todo-num;
    }
  }
}
.current-todolist {
  flex: 1;
  margin: 0 10px;
  .current-todo-info {
    input {
      width: 100%;
      border: none;
      background: transparent;
      &:focus {
        border: none;
        outline: none;
      }
    }
    .nav-tools {
      span,
      button {
        display: inline-block;
        margin: 5px;
        vertical-align: middle;
      }
      button {
        height: 20px;
      }
    }
  }
}
.btn {
  display: inline-block;
  padding: 0.2em 0.3em;
  border-radius: 0.5em;
  border: 1px solid #ddd;
  cursor: pointer;
}
header,
main {
  padding: 10px;
}
header {
  background: linear-gradient(180deg, #d0edf5, #e1e5f0);
}
main {
  background-color: #fff;
}
.tool-tips {
  display: flex;
  justify-content: flex-start;
  & > div {
    margin: 0 0.5%;
    vertical-align: middle;
  }
  .check-all {
    input,
    label {
      display: inline-block;
      vertical-align: middle;
    }
  }
}
.todo-list-wrap {
  li {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 0;
    cursor: move;
    @at-root {
      input[type="checkbox"] {
        display: inline-block;
        width: 20px;
        height: 20px;
        cursor: pointer;
      }
    }
    input[type="text"] {
      padding: 0 0.5%;
      flex: 1;
      height: 20px;
      outline: none;
      border: none;
    }
    button {
      width: 20px;
      background: transparent;
      border: none;
      cursor: pointer;
    }
  }
  .locked {
    input[type="text"],
    input[type="checkbox"] {
    }
  }
}
.finished {
  .active {
    text-decoration: line-through;
  }
}
</style>
