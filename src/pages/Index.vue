<template>
  <q-page class="q-pa-lg">
    <div class="container bg-grey-4 full-height">
      <h5>Upload your exported txt file:</h5>
      <div class="q-pa-md">
        <div class="q-gutter-md" style="max-width: 300px">
          <label class="text-reader">
            Read File
            <input type="file" @change="loadTextFromFile" accept=".txt">
          </label>
        </div>
      </div>
      <p v-if="chatData.message">cac</p>
    </div>
  </q-page>
</template>

<script>
export default {
  data () {
    return {
      chatData: [
        {
          date: '',
          time: '',
          sender: '',
          message: ''
        }
      ],
      file: ''
    }
  },
  methods: {
    loadTextFromFile (e) {
      // var chatData = [
      //   {
      //     date: '',
      //     time: '',
      //     sender: '',
      //     message: ''
      //   }
      // ]
      const file = e.target.files[0]
      const reader = new FileReader()
      reader.onload = e => {
        var lines = reader.result.split('\n')
        // var formatedDate = []
        // var formatedTime = []
        // var formatedSender = []
        // var formatedMessage = []
        // filter message data line by line from .txt file
        for (var i = 1; i < lines.length - 1; i++) {
          if (lines[i].match(/([0-9])\d,/g)) {
            // extract date
            var oringinalDate = lines[i].split(',')
            // // extract time
            // formatedDate.push(oringinalDate[0])
            // formatedTime.push(oringinalDate[1].split(' ')[1] + ' ' + oringinalDate[1].split(' ')[2])
            // // extract sender
            // formatedSender.push(lines[i].split('- ')[1].split(': ')[0])
            // // extract message
            // formatedMessage.push(lines[i].split('- ')[1].split(': ')[1])
            this.chatData.push({
              date: oringinalDate[0],
              time: oringinalDate[1].split(' ')[1] + ' ' + oringinalDate[1].split(' ')[2],
              sender: lines[i].split('- ')[1].split(': ')[0],
              message: lines[i].split('- ')[1].split(': ')[1]
            })
          }
        }
        // analize filtered message data
        // for (let i = 0; i < this.chatData.length; i++) {
        // }
      }
      console.log(this.chatData)
      reader.readAsText(file)
    }
  }
}
</script>

<style scoped>
.container {
  margin: 0 auto;
  width: 70%;
}

.text-reader {
  position: relative;
  overflow: hidden;
  display: inline-block;

  /* Fancy button looking */
  border: 2px solid black;
  border-radius: 5px;
  padding: 8px 12px;
  cursor: pointer;
}
</style>
