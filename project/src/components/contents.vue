<template>
  <section class="main" v-show="list.length">
    <!-- 全选按钮 -->
    <input class="toggle-all" type="checkbox" v-model="isAll" />
    <ul class="todo-list">
        <!-- 列表栏  completed editing -->             
        <li :class="{completed:item.chacked,editing:arrDblId===item.id}" v-for="item in list">
            <div class="view">
                <input class="toggle" type="checkbox" v-model="item.chacked"/>
                <label @dblclick="appDbl(item)">{{item.title}}</label>
                <button class="destroy" @click="appRemove(item.id)"></button>
            </div>
            <input 
                class="edit" 
                v-model="item.title"
                @blur="appDone(item)"
                @keydown.13="appDone(item)"
                @keydown.esc="appCancel(item)"
            />
        </li>
    </ul>
  </section>
</template>

<script>

export default {
    props:['list'], 
    data () {
        return {
            arrDblId:'', //记录要编辑的id
            arrContent:''
        }
    },   
    methods:{
        //删除数据
        appRemove (item) {
            console.log(item)
            this.$emit('remove',item)
        },
        //双击事件
        appDbl (item) {
            console.log(1)
            this.arrDblId = item.id;

           //保存一下当前要编辑栏的数据
            this.arrContent = item.title;
        },
        //编辑
        appDone (item) {
            //编辑完成标题呈现，输入框隐藏
            this.arrDblId = ''
            //如果标题为空删除当前行
            if(item.title.trim()==''){
                this.appRemove (item.id)
            }
        },
        //取消编辑
        appCancel (item) {
            this.arrDblId = '';

            item.title = this.arrContent;
            this.arrContent = '';
        }

    },
    computed:{
       isAll : {
           get () {
               let newList = this.list.filter(function(item){
                   return item.chacked
               })
               return newList.length === this.list.length
           },
            set (newValue) {
                this.list.forEach(function(item){
                    item.chacked = newValue;
                })
            }
       } 
    }  
}
</script>

