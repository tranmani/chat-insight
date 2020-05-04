<template>
  <q-page class="q-pa-lg bg-grey-3">
    <section id="main_section" class="q-pt-xl">
      <main id="start">
        <h5 id="upload">Upload your exported txt file</h5>
        <div class="q-pa-md">
          <div class="center" style="max-width: 300px">
            <breeding-rhombus-spinner
              :animation-duration="1500"
              :size="60"
              :color="'#1976d2'"
              class="center"
              v-if="isUploading"
            />
            <label class="text-reader" v-ripple.early v-if="!isUploading">
              Upload File
              <input type="file" @change="AnalyzeTextFromFile" accept=".txt" hidden>
            </label>
            <q-item id="clickMeSenpai" to='/result' class="hide"></q-item>
          </div>
        </div>
      </main>
    </section>

    <section id="main_section">
      <main id="start">
        <h5 id="howto">How to</h5>
        <a href="#" v-scroll-to="'#upload'">Scroll to #element</a>

      </main>
    </section>

    <section id="main_section">
      <main id="start">
        <h5 id="faq">FAQ</h5>
        <div class="row">
          <div class="col-xs-12 col-sm-6">
            <h4>
              Do you support other chat service beside Whatsapp?
            </h4>
            <p>
              Currently no, but in the future if I have time then yes
            </p>
          </div>
          <div class="col-xs-12 col-sm-6">
            <h4>
              Do you support other chat service beside Whatsapp?
            </h4>
            <p>
              Currently no, but in the future if I have time then yes
            </p>
          </div>

          <div class="col-xs-12 col-sm-6">
            <h4>
              Do you support other chat service beside Whatsapp?
            </h4>
            <p>
              Currently no, but in the future if I have time then yes
            </p>
          </div>
          <div class="col-xs-12 col-sm-6">
            <h4>
              Do you support other chat service beside Whatsapp?
            </h4>
            <p>
              Currently no, but in the future if I have time then yes
            </p>
          </div>

          <div class="col-xs-12 col-sm-6">
            <h4>
              Do you support other chat service beside Whatsapp?
            </h4>
            <p>
              Currently no, but in the future if I have time then yes
            </p>
          </div>
          <div class="col-xs-12 col-sm-6">
            <h4>
              Do you support other chat service beside Whatsapp?
            </h4>
            <p>
              Currently no, but in the future if I have time then yes
            </p>
          </div>
        </div>
      </main>
    </section>
  </q-page>
</template>

<script>
import { LocalStorage } from 'quasar'
import { BreedingRhombusSpinner } from 'epic-spinners'
/* eslint-disable no-unused-vars */
/* eslint-disable no-prototype-builtins */
/* eslint-disable no-trailing-spaces */
/* eslint-disable no-useless-escape */
export default {
  components: {
    BreedingRhombusSpinner
  },
  created () {
    this.isUploading = false
  },
  data () {
    return {
      chatData: {
        chatName: '',
        firstChatDate: '',
        lastChatDate: '',
        participants: [],
        wordOccurrence: [],
        emojiOccurrence: [],
        dateOccurrence: [],
        timeOccurrence: [],
        totalMessage: '',
        totalImage: '',
        totalLink: '',
        totalEmoji: ''
      },
      isUploading: false,
      isDone: false
    }
  },
  methods: {
    AnalyzeTextFromFile (e) {
      this.isUploading = true
      setTimeout(function () {
        // window.location.href = '/#/result'
        document.getElementById('clickMeSenpai').click()
      }, 1800)
      const file = e.target.files[0]
      const reader = new FileReader()
      reader.onload = e => {
        var filteredChatData = []
        var lines = reader.result.split('\n')
        var totalImage = 0
        var totalLink = 0
        var timeArr = []
        var chatName

        // filter message data line by line from .txt file
        for (var i = 1; i < lines.length - 1; i++) {
          // get group name
          if (lines[1].includes(' created group ', 17)) {
            chatName = lines[1].split('"')[1]
          }

          // get total hyperlink in the whole chat
          if (lines[i].match(/https?:\/\/(www\.)?[-a-zA-Z0-9@:%._\+~#=]{1,256}\.[a-zA-Z0-9()]{1,6}\b([-a-zA-Z0-9()@:%_\+.~#?&//=]*)/g)) {
            totalLink++
          }

          // get total image in the whole chat
          if (lines[i].includes('<Media omitted>', 17)) {
            totalImage++
          }

          // filter out line that is not a proper chat message
          if (lines[i].match(/([0-9]+\/[0-9]+\/[0-9]+)\d,/g) && !lines[i].includes('Messages to this group are now secured with end-to-end encryption. Tap for more info.', 17) && !lines[i].includes(' created group ', 17) && !lines[i].includes(' added ', 17) && !lines[i].includes(' left', 17) && !lines[i].includes(" changed this group's icon", 17) && !lines[i].includes(' changed the subject from ', 17) && !lines[i].includes(' removed ', 17) && !lines[i].includes(" You're now an admin", 17) && !lines[i].includes(' changed to ', 17) && !lines[i].includes(' changed the group description', 17) && !lines[i].includes(' This message was deleted', 17)) {
            // extract date
            var oringinalDate = lines[i].split(',')
            filteredChatData.push({
              date: oringinalDate[0],
              time: oringinalDate[1].split(' ')[1] + ' ' + oringinalDate[1].split(' ')[2],
              sender: lines[i].split('- ')[1].split(': ')[0],
              message: lines[i].split('- ')[1].split(': ')[1]
            })
            timeArr.push(oringinalDate[1].split(' ')[1].split(':')[0] + ' ' + oringinalDate[1].split(' ')[2])
          }
        }

        // analyze filtered message data
        var firstChatDate = filteredChatData[0].date
        var lastChatDate = filteredChatData[filteredChatData.length - 1].date
        var totalMessage = filteredChatData.length
        var participants = [] // name and number of messages
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
          if (Object.keys(participantsObject).length === 2) {
            chatName = Object.keys(participantsObject)[0] + ' and ' + Object.keys(participantsObject)[1]
          }
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
            if (!emojis[j].match(/’/g)) {
              if (emojis[j].match(/(\u00a9|\u00ae|[\u2000-\u3300]|\ud83c[\ud000-\udfff]|\ud83d[\ud000-\udfff]|\ud83e[\ud000-\udfff])/g)) {
                emojisArr.push(emojis[j])
              }
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
        totalEmoji = emojisArr.length

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
        
        // sort array in decending order
        wordOccurrence.sort(compare)
        emojiOccurrence.sort(compare)
        dateOccurrence.sort(compare)
        timeOccurrence.sort(compare)
        participants.sort(compare2)

        // assign all extracted value and put it to localstorage
        this.chatData.chatName = chatName
        this.chatData.firstChatDate = firstChatDate
        this.chatData.lastChatDate = lastChatDate
        this.chatData.totalMessage = totalMessage
        this.chatData.totalWord = totalWord
        this.chatData.totalEmoji = totalEmoji
        this.chatData.totalLink = totalLink
        this.chatData.totalImage = totalImage
        this.chatData.participants = [...participants]
        this.chatData.wordOccurrence = [...wordOccurrence]
        this.chatData.emojiOccurrence = [...emojiOccurrence]
        this.chatData.dateOccurrence = [...dateOccurrence]
        this.chatData.timeOccurrence = [...timeOccurrence]

        const chatData = {}
        LocalStorage.set(chatData, this.chatData)

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
        function compare2 (a, b) {
          // decending sort
          if (a.messages > b.messages) return -1
          if (b.messages > a.messages) return 1
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
    },
    redirect (page) {
      window.location.href = page
    }
  }
}
</script>

<style scoped>
.hide {
  width: 1px;
  height: 1px;
}
</style>
