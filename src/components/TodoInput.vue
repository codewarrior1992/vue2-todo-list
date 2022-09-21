<template>
  <div class="input-group mb-3">
      <span class="input-group-text">請輸入你的代辦事項</span>
      <input type="text" class="form-control" placeholder="準備要做的任務" v-model.trim="todo" @keyup.enter="add">
      <button class="btn btn-outline-secondary" type="button" @click.prevent="add">新增</button>
  </div>
</template>

<script>
export default {
  data(){
    return {
      todo :''
    }
  },
  methods:{
    add(){
      this.$emit('add',this.todo);
      this.todo = '';
    }
  },
  watch:{

  }
}
</script>
