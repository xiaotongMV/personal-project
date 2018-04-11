<template>
  <footer class="footer" v-show="list.length">
    <span class="todo-count">
        <strong>{{isListLength}}</strong>
        <span>条未选中</span>
    </span>
    <ul class="filters">
        <li><a href="#/all" class="selected">All</a></li> 
        <li><a href="#/active" class="">Active</a></li> 
        <li><a href="#/completed" class="">Completed</a></li>
    </ul>
  </footer>
</template>

<script>
export default {
  props:['list'],
  computed:{
    isListLength () {
      return this.list.filter(function(item){
        return !item.chacked;
      }).length
    }
  }
  
}
</script>

