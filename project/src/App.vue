<template>
  <div id="app">
    <section class="todoapp" id="app">
      <header-input @add="todoList"></header-input>
      <contents-list 
        :list="list" 
        @remove="todoRemove"
      ></contents-list>
      <foorter-num :list="list"></foorter-num>
    </section>
  </div>
</template>

<script>

import headerInput from "./components/headers";
import contentsList from "./components/contents";
import foorterNum from "./components/foorters";

let data = [
    
]
export default {
  data () {
    return {
      list:data,      
    }
  },
  methods:{
    //添加数据
    todoList (message) {
      //console.log('父级',message)
      this.list.push({
        id:new Date () + Math.random(),
        title:message,
        chacked:false
      })      
    },
    //删除数据
    todoRemove (id) {
      //console.log('父级',id)
       this.list = this.list.filter(function(item){
        return item.id !== id;
      })
    }
  },
  components:{
    headerInput,
    contentsList,
    foorterNum
  }
}
</script>

