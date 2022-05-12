<template>
  <div id="app">
    <div class="left">
      <label for="word">
        <textarea id="word" placeholder=" 请输入文本..." v-model="text"></textarea>
      </label>
    </div>
    <div class="right">
      <div class="btn">
        <el-button @click="setupStartIndex">设置起点</el-button>
      </div>
      <div class="btn">
        <el-button @click="read">开始朗读</el-button>
      </div>
      <div class="btn">
        <el-button @click="stop">停止朗读</el-button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      text: '',
      startIndex: 0,
      voice: undefined,
    }
  },
  computed: {
    readText: function() {
      const box = document.querySelector('#word')
      const len = box.textLength
      return this.text.slice(this.startIndex, len) 
    }
  },
  methods: {
    setupStartIndex() {
      const box = document.querySelector('#word')
      const start = box.selectionStart
      this.startIndex = start
      this.$message({
        message: '设置成功',
        type: 'success',
        duration: '600'
      });
    },
    read() {
      let msg = new SpeechSynthesisUtterance();
      msg.text = this.readText
      msg.voice = this.voice
      window.speechSynthesis.speak(msg);
      let index = this.startIndex
      msg.onboundary = function(event) {
        const box = document.querySelector('#word')
        box.focus()
        box.setSelectionRange(index + event.charIndex, index + event.charIndex + event.charLength)
      }
    },
    stop() {
      window.speechSynthesis.cancel();
    },
  },
  mounted() {
    window.speechSynthesis.onvoiceschanged = function() {
      this.voice = window.speechSynthesis.getVoices()[0]
    }
  }
}
</script>

<style>
.left {
  position: absolute;
  top: 5px;
  left: 5px;
}
.right {
  position: absolute;
  top: 5px;
  left: 580px;
  padding-top: 100px;
}
#word {
  resize: none;
  border: 1px solid black;
  height: 600px;
  width: 550px;
  outline:none;
  font-size: 17px;
}
.btn {
  margin: 20px 0;
}
</style>
