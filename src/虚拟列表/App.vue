<template>
  <h2>
    {{ msg }}
  </h2>
  <hr>

  
<form onsubmit="return false" ref="myForm">
  <div>
    标题: <input type="text" placeholder="请输入代办标题" v-model="title" >
    日期: <input type="date" v-model="date">
  </div>
  <button @click="commit(id)">提交</button><br>
  <button @click="add">添加</button>
</form>



  <!--建一个表格，表头依次是标题，日期，操作，单元格之间存在边框 -->
    <table border="1" >
      <tr style="display:fixed ;">
        <th>标题</th>
        <th>日期</th>
        <th>操作</th>
      </tr>
      <!-- 用v-for循环遍历数组，每个数组元素都是一个对象，对象中有title和date两个属性 -->
      <tr v-for="item in list " :key="item.id" >
        <!-- 用v-bind绑定title和date属性 -->
        <td>{{ item.title }}</td>
        <td>{{ item.date }}</td>
        <!-- 用v-on绑定点击事件，点击删除按钮时，调用deleteItem方法 -->
        <td><button @click="edit(item.id)" >编辑</button>
          <button @click="delItem(item.id)">删除</button>
        </td>
      </tr>

      <!-- 虚拟列表 -->

      <div class="container"  
      style="overflow-y: auto;
      height:300px;"  @scroll="onScroll">
      <div class="content" :style="{height:arr.length * 30 + 'px'}">
      <div class="placeholder" :style="{height:indexCurrent  * 30 + 'px'}"></div>
        <tr v-for="item in newArr " :key="item.id" style="height:30px ;">
        <!-- 用v-bind绑定title和date属性 -->
        <td>{{ item.title }}</td>
        <td>{{ item.date}}</td>
        <!-- 用v-on绑定点击事件，点击删除按钮时，调用deleteItem方法 -->

      </tr>
    </div>
    </div>
    </table>

  </template>
  
  <script>
  export default {
  
    data(){
      return {
        title: '',
        date: '',
        id: 0 ,
        list: [],
        status: false,
        indexCurrent:'',
        arr:[]
      }
    },
    created(){
      //一开始创建一个数组，从1到1000,并且id为时间戳

        for(let i = 0; i < 1000; i++){
          this.arr.push({
            id: Date.now() + i,
            title: '标题' + i,
            date: '2020-01-01'
          })
          }



      //从本地储存中获取id数据，如果存在id，则使用数组中最后一位元素的id，如果不存在id，则使用0

      if(localStorage.getItem('list')){
      const length = JSON.parse(localStorage.getItem('list')).length
      console.log(length)
      this.id = JSON.parse(localStorage.getItem('list'))[length - 1].id + 1
      }
     else{
       this.id = 0
     }
    },
    mounted(){
      // 从本地存储中获取数据
      let list = localStorage.getItem('list')
      // 如果本地存储中有数据，就把数据赋值给list
      if(list){
        this.list = JSON.parse(list)
      }
    },
    methods:{
      
      // 监听表格的滚动事件，获取当前滚动的位置
      onScroll(e){
        console.log(e)
      const scrollTop = e.target.scrollTop
        console.log(scrollTop)
        // 通过scrollTop计算当前滚动到第几个元素
  
       this.indexCurrent = Math.floor(scrollTop / 30)
        //q:如何在标签中的style使用绑定indexCurrent的值
        //a:使用v-bind:style="height:{{indexCurrent}} * 30 +'px'"
        
      },


      commit(id){
         //如果title和date任一为空，结束运行
         if(!this.title || !this.date){
          //清空表单
          alert('请按要求填写表单')
          return
        }
        this.list[id].title = this.title
        this.list[id].date = this.date
        localStorage.setItem('list', JSON.stringify(this.list))
        this.title = ''
        this.date = '',
        this.status = false
      },
      add(){
        // 如果title和date任一为空，就不添加，并弹出弹窗信息:请输入标题和日期
        if(!this.title || !this.date){
          alert('请输入标题和日期')
          return
        }
        this.list.push({
          title: this.title,
          date: this.date,
          id: this.id++

        })
        localStorage.setItem('list', JSON.stringify(this.list))
        // 
        this.title = ''
        this.date = ''
        
      },
      edit(id){
       
        this.title = this.list.filter(item => item.id === id)[0].title
        this.date = this.list.filter(item => item.id === id)[0].date
        this.id = id
        this.status = true
      },
      delItem(id){
        const currentIndex = this.list.findIndex(item => item.id === id)
        this.list.splice(currentIndex, 1)
        localStorage.setItem('list', JSON.stringify(this.list))
      },
      // 
      
    },
      computed:{
        msg(){
          return this.status ? `编辑:${this.title}` : '添加'
        },
        newArr(){
          return this.arr.slice(this.indexCurrent, this.indexCurrent + 10)
        }
      }
    }
  
  </script>

  <style  scoped>
  button{
    border-radius: 10px;
    height: 25px;
    font-size: 12px;
    background-color: #efefef;
  }
 *{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
 }
</style>