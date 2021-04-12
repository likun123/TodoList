<template>
  <li
    @dragstart="dragStart(item)"
    @dragenter="dragEnter(item)"
    @dragover.prevent="dragOver"
    @dragend="dragEnd(item)"
    :class="{ active: item.isFinished }"
  >
    <input
      v-bind:disabled="isLocked || item.isLocked"
      v-model="item.isFinished"
      type="checkbox"
    />
    <input
      v-bind:disabled="isLocked || item.isLocked"
      v-model="item.text"
      type="text"
    />
    <button
      v-bind:disabled="isLocked || item.isLocked"
      @click="removeItem(item)"
    >
      X
    </button>
    <span
      class="is-locked"
      @click="isLockedSubItem(item)"
      v-bind:class="{ active: item.isLocked || item.isLocked }"
    >
    </span>
    
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
    isLocked: {
      type: Boolean,
    },
  },
  data() {
    return {};
  },
  methods: {
    removeItem(item) {
      this.$emit("removeItem", item);
    },
    dragStart(item) {
      this.$emit("dragstart", item);
    },
    dragEnter(item) {
      this.$emit("dragenter", item);
    },
    dragOver() {
      this.$emit("dragover");
    },
    dragEnd(item) {
      this.$emit("dragend", item);
    },
    isLockedSubItem(item) {
      this.$emit("isLockedSubItem", item);
    },
  },
};
</script>
<style>
</style>
