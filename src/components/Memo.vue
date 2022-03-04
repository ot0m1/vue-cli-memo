<template>
  <div id="header"><h1>{{ msg }}</h1></div>
  <div id="container">
    <div id="left-field">
      <ul>
        <li v-for="(memo, index) in memos" :key="memo.firstLine">
          <a href="#" @click="startEditing(index)">{{ memo.firstLine }}</a>
        </li>
        <li v-show="!memos.length">メモはありません</li>
      </ul>
      <button type="button" @click="startAdding">+</button>
    </div>
    <div id="right-field">
      <template v-if="addingFlag">
        <form @submit.prevent="addMemo">
          <textarea v-model="newMemo" required></textarea>
          <input type="submit" value="追加">
          <button type="button" @click="cancel">キャンセル</button>
        </form>
      </template>
      <template v-else-if="!addingFlag && displayDetailIndex !== null">
        <form @submit.prevent="editMemo">
          <textarea v-bind:value="memos[displayDetailIndex].content" v-on:input="editedMemo = $event.target.value" required></textarea>
          <input type="submit" value="編集">
          <button type="button" @click="deleteMemo()">削除</button>
          <button type="button" @click="cancel">キャンセル</button>
        </form>
      </template>
      <template v-else></template>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Memo',
  props: {
    msg: String
  },
  data () {
    return {
      memos: [],
      newMemo: '',
      editedMemo: '',
      addingFlag: false,
      displayDetailIndex: null
    }
  },
  mounted: function () {
    this.memos = JSON.parse(localStorage.getItem('memos')) || []
  },
  methods: {
    startAdding () {
      this.addingFlag = true
      this.displayDetailIndex = null
    },
    endAdding () {
      this.addingFlag = false
    },
    startEditing (index) {
      console.log(index)
      this.addingFlag = false
      this.displayDetailIndex = index
    },
    endEditing () {
      this.displayDetailIndex = null
    },
    addMemo () {
      const firstLine = this.firstLine(this.newMemo)
      const item = { firstLine: firstLine, content: this.newMemo}
      this.memos.push(item)
      this.clearMemo()
      this.endAdding()
      this.saveMemo()
    },
    editMemo () {
      const firstLine = this.firstLine(this.editedMemo)
      const item = { firstLine: firstLine, content: this.editedMemo}
      this.memos[this.displayDetailIndex] = item
      this.clearMemo()
      this.endEditing()
      this.saveMemo()
    },
    deleteMemo () {
      if (confirm('削除してよろしいですか？')) {
        this.memos.splice(this.displayDetailIndex, 1)
        this.clearMemo()
        this.endEditing()
        this.saveMemo()
      }
    },
    cancel () {
      this.clearMemo()
      this.endAdding()
      this.endEditing()
    },
    clearMemo () {
      this.editedMemo = ''
      this.newMemo = ''
    },
    saveMemo () {
      localStorage.setItem('memos', JSON.stringify(this.memos))
    },
    firstLine (content) {
      return content.split(/\r\n|\n/)[0]
    }
  }
}
</script>

<style scoped>
h1 {
  text-align: center;
}

#container {
  width: 60%;
  max-width: 600px;
  margin: 0 auto;
  display: flex;
}

#left-field {
  width: 30%;
}

#left-field ul {
  list-style: none;
  margin: 0 0 8px 0;
  padding: 0;
}

#left-field li {
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

#left-field a {
  text-decoration: none;
}

#right-field {
  width: 70%;
  min-height: 300px;
  border: 1px solid #e2e2e2;
  border-radius: 5px;
  padding-top: 10px;
}

#right-field form {
  width: 60%;
  display: block;
  margin: 0 auto;
} 

#right-field textarea {
  width: 90%;
  min-height: 200px;
  display: block;
  border: 1px solid #a9a9a9;
  border-radius: 5px;
  padding: 5px;
  margin-bottom: 10px;
}

* input[type="submit"] {
  margin-right: 5px;
  cursor: pointer;
}

* button {
  margin-right: 5px;
  cursor: pointer;
}
</style>
