<template>
  <li
    @dragstart="dragStart(item)"
    @dragenter="dragEnter(item,)"
    @dragover.prevent="dragOver"
    @dragend="dragEnd(item)"
    :class="{ active: item.isFinished }"
  >
    <input
      v-model="item.isFinished"
      type="checkbox"
    />
    <input
      v-model="item.text"
      type="text"
    />
    <button @click="removeItem(item)">X</button>
  </li>
</template>
<script>
export default {
  name: "todoItem",
  props: {
    item: {
      type: Object,
      required: true,
    },
  },
  data() {
    return {};
  },
  methods: {
    removeItem(item) {
      this.$emit("removeItem", item);
    },
    dragStart(item){
      this.$emit("dragstart", item)
    },
    dragEnter(item){
      this.$emit("dragenter", item)
    },
    dragOver(item){
      this.$emit("dragover", item)
    },
    dragEnd(item){
      this.$emit("dragend", item)
    }
  },
};
</script>
<style>
</style>
