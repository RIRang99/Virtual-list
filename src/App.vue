<script setup>
  import {ref,reactive,watch} from 'vue'
  const list = reactive( JSON.parse(localStorage.getItem('list') || '[]'))
  const form = reactive({
    date:'',
    title:''
  })

  const select = ref(-1)
  //提交功能，点击提交按钮，将表单中的数据添加到数组中
  const commit = () => {
    //判断表单内容是否为空,如果为空，不添加，并弹出对话框“请输入标题和日期”
    if(form.date === '' || form.title === ''){
      alert('请输入标题和日期')
      return
    }
   else if(select.value === -1){
      list.push({
        id:Date.now(),
        date:form.date,
        title:form.title
      })
    }else{
      list[select.value].date = form.date
      list[select.value].title = form.title
    }
    select.value = -1
    form.date = ''
    form.title = ''
  }
  //编辑功能,点击编辑按钮，将当前行的标题和日期显示在表单中
  const edit = (index, title, date) => {
    select.value = index
    form.title = title
    form.date = date
    select.value = index
  }
  //删除功能，点击删除按钮，将当前行的数据从数组中删除
  const delItem = (id) => {
    const index = list.findIndex(item => item.id === id)
    list.splice(index,1)
  }
  //监听list的变化，当list发生变化时，将list存储到localStorage中
  watch(list,()=>{
    localStorage.setItem('list',JSON.stringify(list))
  })

// 拖拽功能
const handleDragStart = (e, key) => {
  e.dataTransfer.setData('key', key)
}
const handleDragOver = (e, key) => {
  e.preventDefault()
}
const handleDrop = (e, key) => {
  const fromKey = e.dataTransfer.getData('key')
  const fromItem = list[fromKey]
  const toItem = list[key]
  list.splice(fromKey, 1, toItem)
  list.splice(key, 1, fromItem)
}


</script>

<template>
  <!-- 设计一个表格，表头具有标题，日期，操作，其中操作单元格内包含编辑和删除两个按钮 -->
    <form onsubmit="return false">
      <h3>{{ select===-1?'添加':`编辑:${form.title}` }}</h3>
      <div>
        标题: <input type="text" placeholder="请输入代办标题" v-model="form.title" >
        日期: <input type="date" v-model="form.date">
      </div>
      <div style="margin:10px 0 ;">
        <button @click="commit()">提交</button>
      <button @click="selected = -1; form.title = ''; form.date = ''">添加</button>
      </div>
 
    </form>
    <!--建一个表格，表头依次是标题，日期，操作，单元格之间存在边框 -->
    <table border="1" >
      <tr style="display:fixed ;">
        <th>标题</th>
        <th>日期</th>
        <th>操作</th>
      </tr>
      <!-- 用v-for循环遍历数组，每个数组元素都是一个对象，对象中有title和date两个属性 -->
      <tr v-for=" ( item,index) in list " :key="item.id" draggable="true" @dragstart="handleDragStart($event, index)" @dragover="handleDragOver($event,index)" @drop="handleDrop($event, index)">
        <!-- 用v-bind绑定title和date属性 -->
        <td>{{ item.title }}</td>
        <td>{{ item.date }}</td>
        <!-- 用v-on绑定点击事件，点击删除按钮时，调用deleteItem方法 -->
        <td><button @click="edit(index, item.title, item.date)" >编辑</button>
          <button @click="delItem(item.id)">删除</button>
        </td>
      </tr>
    </table>
</template>

<style scoped>
  button{
    width: 60px;
    height: 30px;
    background-color: #efefef;
    font-size: 14px;
    padding: 0px 10px;
  }
</style>