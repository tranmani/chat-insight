<template>
  <q-page class="q-pa-lg bg-grey-3">
    <section id="main_section" class="q-pt-xl">
      <main id="start">
        <h5>Upload your exported txt file:</h5>
        <div class="q-pa-md">
          <div class="q-pa-md absolute-center q-mt-xl" style="max-width: 300px">
            <label class="text-reader">
              Read File
              <input type="file" @change="AnalyzeTextFromFile" accept=".txt" hidden>
            </label>
          </div>
        </div>
        <p v-if="chatData.message">cac</p>
      </main>
    </section>
  </q-page>
</template>

<script>
/* eslint-disable no-unused-vars */
/* eslint-disable no-prototype-builtins */
/* eslint-disable no-trailing-spaces */
/* eslint-disable no-useless-escape */
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
      ]
    }
  },
  methods: {
    AnalyzeTextFromFile (e) {
      const file = e.target.files[0]
      const reader = new FileReader()
      reader.onload = e => {
        var filteredChatData = []
        var lines = reader.result.split('\n')
        var totalImage = 0
        var totalLink = 0
        var timeArr = []

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
            timeArr.push(oringinalDate[1].split(' ')[1].split(':')[0] + ' ' + oringinalDate[1].split(' ')[2])
          }

          // get total link in the whole chat
          if (lines[i].match(/https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)/g)) {
            totalLink++
          }
        }
        console.log(totalLink)
        console.log(totalImage)

        // analyze filtered message data
        var firstChatDate = filteredChatData[0].date
        var lastChatDate = filteredChatData[filteredChatData.length - 1].date
        var totalMessage = filteredChatData.length
        var participants = [] // name and number of mesages
        var wordOccurrence = [] // word and number of occurrence
        var emojiOccurrence = [] // emoji and number of occurrence
        var dateOccurrence = [] // date and number of occurrence
        var timeOccurrence = [] // time and number of occurrence
        var totalWord
        var totalEmoji
        
        // messages by sender counter
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
        var dateArr = []
        for (let i = 0; i < filteredChatData.length; i++) {
          var words = []
          var emojis = []
          dateArr.push(filteredChatData[i].date)
          for (let j = 0; j < filteredChatData[i].message.split(' ').length; j++) {
            // filter out media file
            if (!filteredChatData[i].message.includes('<Media omitted>')) {
              words.push(filteredChatData[i].message.split(' ')[j])
            }
            if (filteredChatData[i].message.match(/(\u00a9|\u00ae|[\u2000-\u3300]|\ud83c[\ud000-\udfff]|\ud83d[\ud000-\udfff]|\ud83e[\ud000-\udfff])/g)) {
              var lineEmoji = emojiStringToArray(filteredChatData[i].message.split(' ')[j])
              for (let k = 0; k < lineEmoji.length; k++) {
                emojis.push(lineEmoji[k])
              }
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
            if (emojis[j].match(/(\u00a9|\u00ae|[\u2000-\u3300]|\ud83c[\ud000-\udfff]|\ud83d[\ud000-\udfff]|\ud83e[\ud000-\udfff])/g) && !emojis[j].match(/[’€]/g)) {
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

        // date occurrence counter
        var dateCounts = {}
        for (let i = 0; i < filteredChatData.length; i++) {
          if (!dateCounts.hasOwnProperty(dateArr[i])) {
            dateCounts[dateArr[i]] = 1
          } else {
            dateCounts[dateArr[i]]++
          }
        }

        for (let i = 0; i < Object.keys(dateCounts).length; i++) {
          dateOccurrence.push({
            date: Object.keys(dateCounts)[i],
            occurrence: Object.values(dateCounts)[i]
          })
        }

        // time occurrence counter
        var timeCounts = {}
        for (let i = 0; i < timeArr.length; i++) {
          if (!timeCounts.hasOwnProperty(timeArr[i])) {
            timeCounts[timeArr[i]] = 1
          } else {
            timeCounts[timeArr[i]]++
          }
        }

        for (let i = 0; i < Object.keys(timeCounts).length; i++) {
          timeOccurrence.push({
            time: Object.keys(timeCounts)[i],
            occurrence: Object.values(timeCounts)[i]
          })
        }

        wordOccurrence.sort(compare)
        emojiOccurrence.sort(compare)
        dateOccurrence.sort(compare)
        timeOccurrence.sort(compare)

        function count (arr, classifier) {
          classifier = classifier || String
          return arr.reduce(function (counter, item) {
            var p = classifier(item)
            counter[p] = counter.hasOwnProperty(p) ? counter[p] + 1 : 1
            return counter
          }, {})
        }

        function compare (a, b) {
          // decending sort
          if (a.occurrence > b.occurrence) return -1
          if (b.occurrence > a.occurrence) return 1

          return 0
        }

        function emojiStringToArray (str) {
          var split = str.split(/([\uD800-\uDBFF][\uDC00-\uDFFF])/)
          var arr = []
          for (var i = 0; i < split.length; i++) {
            var char = split[i]
            if (char !== '') {
              arr.push(char)
            }
          }
          return arr
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
  font-weight: bold;

  /* Fancy button looking */
  border: 2px solid black;
  border-radius: 5px;
  padding: 16px 25px;
  cursor: pointer;
}

#start {
  text-align: center;
  width: 90%;
  box-sizing: border-box;
  max-width: 1200px;
  margin: 0 auto 80px;
  padding: 40px;
  background: #fff;
  border-radius: 10px;
  box-shadow: 0 3px 10px rgba(0,0,0,0.2);
  min-height: 300px;
  position: relative;
}
</style>
