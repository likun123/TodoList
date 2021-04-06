<template lang="">
    <li draggable="true">
      <input v-model="item.isFinished" type="checkbox" />
      <input
        @keyup.enter="updateItem(item)"
        v-bind:class="{ active: item.isFinished }"
        v-model="item.text"
        type="text"
      />
      <button @click="removeItem(item)">X</button>
    </li>
</template>
<script>
export default {
    name:"todoItem",
    props:{
        item:{
            type:Object,
            required:true
        }
    },
    data(){
        return{

        }
    },
    methods:{
        removeItem(item){
            this.$emit('removeItem',item);
        },
        updateItem(item){
            this.$emit('updateItem',item)
        }
    }
}
</script>
<style>
    

</style>