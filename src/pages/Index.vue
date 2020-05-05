<template>
  <q-page class="q-pa-lg bg-grad">
    <section id="main_section" class="q-pt-xl">
      <main id="start">
        <p class="intro">
          Have you ever wondered what is going on in your whatsapp group chat?, <br>
          who is the most talkactive? how many messages has been sent? <br>
          and which date is the most bustling day?...
        </p>
        <p class="intro">
          With just a few click away you will have those informations on your screen
        </p>
        <h4 id="upload">Upload your exported txt file</h4>
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
      </main>
    </section>

    <section id="main_section">
      <main id="start" style="text-align: left">
        <h4 id="howto" style="text-align: center">How to</h4>
        <ol>
          <li>
            Go to WhatsApp app on your phone and select any chat or group.</li>
          <li>
            Press the three dotted button in the top right corner and select "More"
          </li>
          <li>
            Choose "Email Chat" and select "Without Media" option.
          </li>
          <li>
            Upload it to Drive or email the exported text file to yourself.
          </li>
          <li>
            Download the file to your computer or phone <a href="#" v-scroll-to="'#upload'">and upload it to this website.</a>
          </li>
        </ol>

      </main>
    </section>

    <section id="main_section">
      <main id="start" style="text-align: left">
        <h4 id="faq" style="text-align: center">FAQ</h4>
        <div class="row">
          <div class="col-xs-12 col-sm-6">
            <h6>
              Do you support other chat service beside Whatsapp?
            </h6>
            <p>
              Currently no, but in the future if I have time then yes
            </p>
          </div>
          <div class="col-xs-12 col-sm-6">
            <h6>
              What do you do with my uploaded data?
            </h6>
            <p>
              This website will analyze your uploaded data <br>
              and store the numbers and essestial data in your browser <br>
              in order to construct graphs in the result page
            </p>
          </div>

          <div class="col-xs-12 col-sm-6">
            <h6>
              I am really concern about my chat messages!
            </h6>
            <p>
              I'm just a student unlike Google or Facebook, <br>
              your data is useless to me 🤞
            </p>
          </div>
          <div class="col-xs-12 col-sm-6">
            <h6>
              Are you sure?
            </h6>
            <p>
              Yes, hell yes, chắc vl 🤙
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
import moment from 'moment'
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
      const chatData = {}
      LocalStorage.remove(chatData)
      var isSupported = true
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
        var dateTest = []
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
            // determine if exported .txt file is in right format or not
            if (!moment(lines[i].split(',')[0]).isValid()) {
              this.alert()
              isSupported = false
              break
            }
            
            // parse date and time
            var oringinalDate = lines[i].split(',')
            var oringinalTime = oringinalDate[1].split(' ')[1] + ' ' + oringinalDate[1].split(' ')[2]
            var originalDateTime = oringinalDate[0] + ' ' + oringinalTime

            var parsedDateTime = moment(originalDateTime).format('M/D/YY - h:mm A').split(' - ')

            filteredChatData.push({
              date: parsedDateTime[0],
              time: parsedDateTime[1],
              sender: lines[i].split('- ')[1].split(': ')[0],
              message: lines[i].split('- ')[1].split(': ')[1]
            })
            timeArr.push(parsedDateTime[1])
          }
        }
        if (isSupported) {
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
        }

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
    },
    alert () {
      this.$q.dialog({
        title: 'Not supported format',
        message: 'The Date and Time in your exported .txt file should be in this format: 12/30/20, 11:59 AM'
      })
    }
  }
}
</script>

<style scoped>
.hide {
  width: 1px;
  height: 1px;
}

.intro {
  font-size: 1.1em;
}

.bg-grad {
  height: 100%;
  background-color: rgb(2,0,36);
  background-image: linear-gradient(120deg, #e0c3fc 0%, #8ec5fc 100%);
}

h6 {
  margin: 10px 0;
}
</style>
