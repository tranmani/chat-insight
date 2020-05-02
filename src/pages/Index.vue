<template>
  <q-page class="q-pa-lg">
    <div class="container bg-grey-4 full-height">
      <h5>Upload your exported txt file:</h5>
      <div class="q-pa-md">
        <div class="q-gutter-md" style="max-width: 300px">
          <label class="text-reader">
            Read File
            <input type="file" @change="AnalizeTextFromFile" accept=".txt">
          </label>
        </div>
      </div>
      <p v-if="chatData.message">cac</p>
    </div>
  </q-page>
</template>

<script>
/* eslint-disable no-unused-vars */
/* eslint-disable no-prototype-builtins */
export default {
  data () {
    return {
      chatData: [
        {
          firstChatDate: '',
          lastChatDate: '',
          participants: [
            {
              name: '',
              messages: ''
            }
          ],
          totalMessage: '',
          totalImage: '',
          totalEmoji: ''
        }
      ],
      file: ''
    }
  },
  methods: {
    AnalizeTextFromFile (e) {
      var filteredChatData = []
      const file = e.target.files[0]
      const reader = new FileReader()
      reader.onload = e => {
        var lines = reader.result.split('\n')
        var totalImage = 0
        // filter message data line by line from .txt file
        for (var i = 1; i < lines.length - 1; i++) {
          // filter out line that is not a proper chat message
          if (lines[i].match(/([0-9])\d,/g) && !lines[i].includes('Messages to this group are now secured with end-to-end encryption. Tap for more info.', 17) && !lines[i].includes(' created group ', 17) && !lines[i].includes(' added ', 17) && !lines[i].includes(' left', 17) && !lines[i].includes(" changed this group's icon", 17) && !lines[i].includes(' changed the subject from ', 17) && !lines[i].includes(' removed ', 17) && !lines[i].includes(" You're now an admin", 17)) {
            // extract date
            var oringinalDate = lines[i].split(',')
            filteredChatData.push({
              date: oringinalDate[0],
              time: oringinalDate[1].split(' ')[1] + ' ' + oringinalDate[1].split(' ')[2],
              sender: lines[i].split('- ')[1].split(': ')[0],
              message: lines[i].split('- ')[1].split(': ')[1]
            })
            // get total image in the whole chat
            if (lines[i].split('- ')[1].split(': ')[1].includes('<Media omitted>')) {
              totalImage++
            }
          }
        }
        // analize filtered message data
        // var firstChatDate = filteredChatData.date[0]
        // var lastChatDate = filteredChatData.date[filteredChatData.length - 1]
        // var totalMessage = filteredChatData.length
        var participants = []
        var wordOccurrence = []
        var emojiOccurrence = []
        var totalWord
        var totalEmoji
        var participantsObject = count(filteredChatData, function (item) {
          return item.sender
        })
        for (let i = 0; i < Object.keys(participantsObject).length; i++) {
          participants.push({
            name: Object.keys(participantsObject)[i],
            messages: Object.values(participantsObject)[i]
          })
        }

        // extract word by word from message
        var wordsArr = []
        var emojisArr = []
        for (let i = 0; i < filteredChatData.length; i++) {
          var words = []
          var emojis = []
          for (let j = 0; j < filteredChatData[i].message.split(' ').length; j++) {
            // filter out media file
            if (!filteredChatData[i].message.includes('<Media omitted>')) {
              words.push(filteredChatData[i].message.split(' ')[j])
            }
            if (filteredChatData[i].message.match(/(\u00a9|\u00ae|[\u2000-\u3300]|\ud83c[\ud000-\udfff]|\ud83d[\ud000-\udfff]|\ud83e[\ud000-\udfff])/g)) {
              emojis.push(filteredChatData[i].message.split(' ')[j])
            }
          }
          for (let j = 0; j < words.length; j++) {
            // filter out number and emoji
            if (!words[j].match(/(\u00a9|\u00ae|[\u2000-\u3300]|\ud83c[\ud000-\udfff]|\ud83d[\ud000-\udfff]|\ud83e[\ud000-\udfff])/g) && !words[j].match(/[0-9]/g)) {
              wordsArr.push(words[j])
            }
          }
          for (let j = 0; j < emojis.length; j++) {
            // only take emoji
            if (emojis[j].match(/(\u00a9|\u00ae|[\u2000-\u3300]|\ud83c[\ud000-\udfff]|\ud83d[\ud000-\udfff]|\ud83e[\ud000-\udfff])/g)) {
              emojisArr.push(emojis[j])
            }
          }
        }

        // word occurrence counter
        var wordCounts = {}
        for (let i = 0; i < wordsArr.length; i++) {
          if (!wordCounts.hasOwnProperty(wordsArr[i])) {
            wordCounts[wordsArr[i]] = 1
          } else {
            wordCounts[wordsArr[i]]++
          }
        }

        for (let i = 0; i < Object.keys(wordCounts).length; i++) {
          wordOccurrence.push({
            word: Object.keys(wordCounts)[i],
            occurrence: Object.values(wordCounts)[i]
          })
        }
        totalWord = wordOccurrence.length

        // emoji occurrence counter
        var emojiCounts = {}
        for (let i = 0; i < emojisArr.length; i++) {
          if (!emojiCounts.hasOwnProperty(emojisArr[i])) {
            emojiCounts[emojisArr[i]] = 1
          } else {
            emojiCounts[emojisArr[i]]++
          }
        }

        for (let i = 0; i < Object.keys(emojiCounts).length; i++) {
          emojiOccurrence.push({
            emoji: Object.keys(emojiCounts)[i],
            occurrence: Object.values(emojiCounts)[i]
          })
        }
        totalEmoji = emojiOccurrence.length

        console.log(participants)
        console.log(totalImage)
        console.log(wordOccurrence.sort(compare))
        console.log(emojiOccurrence.sort(compare))

        function count (arr, classifier) {
          classifier = classifier || String
          return arr.reduce(function (counter, item) {
            var p = classifier(item)
            // eslint-disable-next-line no-prototype-builtins
            counter[p] = counter.hasOwnProperty(p) ? counter[p] + 1 : 1
            return counter
          }, {})
        }

        function compare (a, b) {
          if (a.occurrence > b.occurrence) return -1
          if (b.occurrence > a.occurrence) return 1

          return 0
        }
      }
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
