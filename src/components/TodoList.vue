<template>
  <div id="main">
    <div id="header">todos</div>
    <div id="section">
      <input v-model.trim="value"
             placeholder="What needs to be done?"
             @keyup.enter="submitInput($event)"
             v-focus />
      <ul>
        <li v-for="content in contents"
            :key="content.id"
            v-show="content.isShow"
            @dblclick.self="doModify(content)">
          <span :class="{completedColor:content.isComplete}">
            {{content.message | ending}}
          </span>
          <input v-model.trim="modifyValue"
                 @keyup.enter="Modify(content)"
                 v-show="content.isModify"
                 :autofocus="content.aa"
                 ref="willModify"
                 v-focus />
          <button @click="deleteTodo(content)">deleteTodo</button>
          <button @click="isComplete(content)">isComplete</button>
        </li>
      </ul>
    </div>
    <div id="footer">
      <p>
        <span>{{completeNum}}</span> items completed
      </p>
      <button @click="all">All</button>
      <button @click="active">Active</button>
      <button @click="completed">Completed</button>
      <button @click="clearCompleted">Clear completed</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data() {
    return {
      value: '',
      modifyValue: '',
      // 修改内容时，中间存储原始值
      stroage: '',
      completeNum: 0,

      // num表示从开始添加的事件数量
      num: 0,

      // flag 表示1:all ,2:active 3: Complete
      flag: '1',
      contents: [
      ]
    }
  },
  methods: {
    // 点击提交事件
    submitInput() {
      if (this.value === '') {
        return
      }
      let obj = {
        id: parseInt(this.num) + 1,
        message: this.value,
        isComplete: false,
        isShow: true,
        isModify: false,
        aa: false
      }
      this.num += 1
      this.contents.push(obj)
      if (this.flag === '1') {
        this.all()
      } else if (this.flag === '2') {
        this.active()
      } else {
        this.completed()
      }
      this.value = ''
    },

    // 点击完成事件
    isComplete(e) {
      e.isComplete = !e.isComplete
      if (e.isComplete) {
        this.completeNum += 1
      } else {
        this.completeNum -= 1
      }
    },

    // 点击删除事件
    deleteTodo(e) {
      if (e.isComplete === true) {
        this.completeNum -= 1
      }
      this.contents.splice(this.contents.indexOf(e), 1)
    },

    // 显示所有内容
    all() {
      this.flag = '1'
      this.contents.forEach(content => {
        content.isShow = true
      })
    },

    // 只显示未完成内容
    active() {
      this.flag = '2'
      this.contents.forEach(content => {
        if (content.isComplete === false) {
          content.isShow = true
        } else {
          content.isShow = false
        }
      })
    },

    // 只显示完成内容
    completed() {
      this.flag = '3'
      this.contents.forEach(content => {
        if (content.isComplete === true) {
          content.isShow = true
        } else {
          content.isShow = false
        }
      })
    },

    // 删除已完成的内容
    clearCompleted() {
      let newContents = []
      this.contents.forEach(content => {
        if (content.isComplete === true) {
          if (content.isComplete === true) {
            this.completeNum -= 1
          }
        } else {
          newContents.push(content)
        }
      })
      this.contents = newContents
    },

    // 双击进行修改
    doModify(e) {
      this.contents.forEach(content => {
        // this.$set(this.$refs.willModify[parseInt(content.id) - 1], 'autofocus', false)
        content.aa = false
        // this.$refs.willModify[parseInt(content.id) - 1].autofocus = content.aa
        if (content !== e && content.isModify === true) {
          content.message = this.storage
          content.isModify = false
        }
      })
      e.isModify = true
      this.modifyValue = ''
      this.storage = e.message
      this.modifyValue = e.message
      e.message = ''
      e.aa = 'autofocus'

      // 遇到的问题是第二次点击其他输入框时无法自动获取焦点
      // this.$nextTick(() => {
      // DOM 更新了
      // this.$refs.willModify[parseInt(e.id) - 1].autofocus = e.aa
      //  Vue.set(this.$refs.willModify[parseInt(e.id) - 1], 'autofocus', true)

      // })
    },

    // enter键确认修改
    Modify(e) {
      if (this.modifyValue === '') {
        e.message = this.storage
        e.isModify = false
        return
      }
      e.message = this.modifyValue
      this.modifyValue = ''
      e.isModify = false
    }
  },
  directives: {
    focus: {
      // 指令的定义
      inserted: (el) => {
        el.focus()
      }
    }
  },
  filters: {
    ending: (content) => (content + '.')
  },
  computed: {
    noCompleteNum: function () {
      return this.num - this.completeNum
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="stylus">
#main {
  font-size: 30px;
  width: 500px;
  margin: 50px auto;
}

#header {
  font-size: 100px;
}

#section {
  line-height: 30px;
  background: lightgreen;
  border: 1px solid #666;

  input {
    width: 95%;
    height: 30px;
    border-bottom: 1px solid #999;
    background: transparent;
    font-size: 25px;
    color: #333;
    padding: 5px;
  }

  li {
    position: relative;
    overflow: auto;
    padding: 5px;
    border-bottom: 1px solid #999;

    span {
      float: left;
      margin-left: 10px;
    }

    button {
      margin-top: 5px;
      float: right;
    }

    input {
      height: 25px;
      width: 300px;
      position: absolute;
      left: 5px;
      top: 0;
    }
  }
}

#footer {
  margin-top: 20px;
  overflow: hidden;
  background: pink;

  p {
    display: inline-block;
    font-size: 16px;
    color: blue;
    margin-left: 2px;
  }
}

button {
  font-size: 15px;
  color: brown;
  background: #e6e6e6;
  border-radius: 5px;
  margin-left: 7px;
}

.completedColor {
  color: red;
}
</style>
