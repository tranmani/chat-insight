<template>
    <q-page class="q-pa-lg bg-grad">
    <section id="main_section" class="q-pt-xl">
      <main id="start">
        <section style="text-align: center" v-if="hasData">
          <header class="">
            <h2>{{ chatData.chatName }}</h2>
          </header>

          <h3>General</h3>
          <div class="row vertical-middle">
            <ResultItem
            classVal='col-xs-12 col-sm-6'
            :h4Val="chatData.firstChatDate"
            pVal="First chat"/>
            <ResultItem
            classVal='col-xs-12 col-sm-6'
            :h4Val="chatData.lastChatDate"
            pVal="Last chat"/>
          </div>
          <div class="row">
            <ResultItem
            classVal='col-sm-6 col-lg-3 col-xs-12'
            :h4Val="formatNumber(chatData.totalMessage)"
            pVal="Messages"/>
            <ResultItem
            classVal='col-sm-6 col-lg-3 col-xs-12'
            :h4Val="formatNumber(chatData.totalImage)"
            pVal="Pictures"/>
            <ResultItem
            classVal='col-sm-6 col-lg-3 col-xs-12'
            :h4Val="formatNumber(chatData.totalEmoji)"
            pVal="Emojis"/>
            <ResultItem
            classVal='col-sm-6 col-lg-3 col-xs-12'
            :h4Val="formatNumber(chatData.totalLink)"
            pVal="Links"/>
          </div>

          <h3>User</h3>
          <div class="row">
            <ResultItem
            classVal='col-xs-12 col-sm-6 col-md-4'
            :h4Val="chatData.participants.length"
            pVal="Number of users"/>
            <div class="col-xs-12 col-sm-6 col-md-4">
              <h4>{{ chatData.participants[0].name }}</h4>
              <p>
                Is a chatty nature <br> with <strong>{{ formatNumber(chatData.participants[0].messages) }}</strong> messages
              </p>
            </div>
            <div class="col-xs-12 col-sm-12 col-md-4">
              <h4>{{ formatNumber(Math.floor(chatData.totalMessage / chatData.participants.length)) }}</h4>
              <p>Average message per user</p>
            </div>
            <div class="col-xs-12">
              <highcharts :options="topMessagesOptions" v-if="this.topMessagesOptions"></highcharts>
            </div>
          </div>

          <h3>Emojis</h3>
          <div class="row">
            <div class="col-xs-12 col-sm-6 col-md-3">
              <h4>{{ chatData.emojiOccurrence[0].emoji }}</h4>
              <p>Most used Emoji <br> used <strong>{{ chatData.emojiOccurrence[0].occurrence }}</strong> times</p>
            </div>
            <ResultItem
            classVal='col-xs-12 col-sm-6 col-md-3'
            :h4Val="chatData.emojiOccurrence.length"
            pVal="Difference emoji used"/>
            <div class="col-xs-12 col-sm-12 col-md-6">
              <highcharts :options="topEmojisOptions" v-if="this.topEmojisOptions"></highcharts>
            </div>
          </div>

          <h3>Dates and Time</h3>
          <div class="row">
            <div class="col-xs-12 col-sm-4 col-md-4">
              <h4>{{ chatData.timeOccurrence[0].time }}</h4>
              <p>Is the busiest time <br> with <strong>{{ chatData.timeOccurrence[0].occurrence }}</strong> messages</p>
            </div>
            <div class="col-xs-12 col-sm-8 col-md-8">
              <highcharts :options="topTimeOptions" v-if="this.topTimeOptions"></highcharts>
            </div>
            <div class="col-xs-12 col-sm-4 col-md-4">
              <h4>{{ chatData.dateOccurrence[0].date }}</h4>
              <p>Is the busiest day <br> with <strong>{{ chatData.dateOccurrence[0].occurrence }}</strong> messages</p>
            </div>
            <div class="col-xs-12 col-sm-8 col-md-8">
              <highcharts :options="topDatesOptions" v-if="this.topDatesOptions"></highcharts>
            </div>
          </div>

          <h3>Words</h3>
          <div class="row">
            <div class="col-xs-12 col-sm-12 col-md-4">
              <h4>{{ formatNumber(chatData.wordOccurrence.length) }}</h4>
              <p>Number of words used <br> (emojis and numbers are not counted)</p>
            </div>
            <div class="col-xs-12 col-sm-12 col-md-8">
              <highcharts :options="topWordsOptions" v-if="this.topWordsOptions"></highcharts>
            </div>
          </div>
        </section>
      </main>
    </section>
  </q-page>
</template>

<script>
import { LocalStorage } from 'quasar'
/* eslint-disable no-unused-vars */
/* eslint-disable no-prototype-builtins */
/* eslint-disable no-trailing-spaces */
/* eslint-disable no-useless-escape */
/* eslint-disable array-bracket-spacing */
/* eslint-disable quotes */
import Highcharts from 'highcharts'
import ResultItem from 'components/ResultItem.vue'

Highcharts.setOptions({
  legend: {
    enabled: false
  },
  credits: {
    enabled: false
  },
  yAxis: {
    min: 0,
    labels: {
      overflow: 'justify'
    }
  }
})
export default {
  mounted () {
    const chatData = {}
    if (!LocalStorage.getItem(chatData)) {
      this.$router.push('/')
    } else {
      this.chatData = LocalStorage.getItem(chatData)
      this.putData()
      this.hasData = true
    }
  },
  components: {
    ResultItem
  },
  data () {   
    return {
      hasData: false,
      chatData: {},
      topMessagesOptions: {},
      topEmojisOptions: {},
      topWordsOptions: {},
      topDatesOptions: {},
      topTimeOptions: {}
    }
  },
  methods: {
    putData () {
      // for Messages
      var users = []
      var messages = []
      for (const key of this.chatData.participants) {
        users.push(key.name)
        messages.push(key.messages)
      }
      
      this.topMessagesOptions = {
        chart: {
          type: 'column'
        },
        title: {
          text: 'Top story teller in the chat'
        },
        xAxis: {
          categories: users
        },
        yAxis: {
          title: {
            text: 'Number of messages'
          }
        },
        plotOptions: {
          series: {
            colorByPoint: true
          }
        },
        series: [
          {
            name: 'Mesages',
            data: messages,
            color: '#1E90FF'
          }
        ]
      }

      // for emojis
      var emojis = []
      var loopTimes
      if (this.chatData.emojiOccurrence.length < 20) {
        loopTimes = this.chatData.emojiOccurrence.length
      } else {
        loopTimes = 10
      }
      for (let i = 0; i < loopTimes; i++) {
        emojis.push([this.chatData.emojiOccurrence[i].emoji, this.chatData.emojiOccurrence[i].occurrence])
      }

      this.topEmojisOptions = {
        chart: {
          type: 'pie',
          options3d: {
            enabled: true,
            alpha: 45
          }
        },
        title: {
          text: 'Top emoji used in the chat'
        },
        series: [
          {
            name: 'Times used',
            data: emojis
          }
        ]
      }

      // for words
      var words = []
      var wordsOccurrences = []
      var loopTimes1
      if (this.chatData.wordOccurrence.length < 20) {
        loopTimes1 = this.chatData.wordOccurrence.length
      } else {
        loopTimes1 = 20
      }
      for (let i = 0; i < loopTimes1; i++) {
        words.push(this.chatData.wordOccurrence[i].word)
        wordsOccurrences.push(this.chatData.wordOccurrence[i].occurrence)
      }
      
      this.topWordsOptions = {
        chart: {
          type: 'column'
        },
        title: {
          text: 'Top used word in the chat'
        },
        xAxis: {
          categories: words
        },
        yAxis: {
          title: {
            text: 'Number of word occurence'
          }
        },
        series: [
          {
            name: 'Occurence',
            data: wordsOccurrences,
            color: '#FF69B4'
          }
        ]
      }

      // for Time
      var times = []
      var timesOccurrences = []
      var loopTimes2
      if (this.chatData.timeOccurrence.length < 20) {
        loopTimes2 = this.chatData.timeOccurrence.length
      } else {
        loopTimes2 = 20
      }
      for (let i = 0; i < loopTimes2; i++) {
        times.push(this.chatData.timeOccurrence[i].time)
        timesOccurrences.push(this.chatData.timeOccurrence[i].occurrence)
      }
      
      this.topTimeOptions = {
        chart: {
          type: 'column'
        },
        title: {
          text: 'Busiest hour'
        },
        xAxis: {
          categories: times
        },
        yAxis: {
          title: {
            text: 'Number of occurence'
          }
        },
        series: [
          {
            name: 'Mesages',
            data: timesOccurrences,
            color: '#8A2BE2'
          }
        ]
      }

      // for Date
      var dates = []
      var datesOccurrences = []
      var loopTimes3
      if (this.chatData.dateOccurrence.length < 20) {
        loopTimes3 = this.chatData.dateOccurrence.length
      } else {
        loopTimes3 = 20
      }
      for (let i = 0; i < loopTimes3; i++) {
        dates.push(this.chatData.dateOccurrence[i].date)
        datesOccurrences.push(this.chatData.dateOccurrence[i].occurrence)
      }
      
      this.topDatesOptions = {
        chart: {
          type: 'column'
        },
        title: {
          text: 'Busiest day'
        },
        xAxis: {
          categories: dates
        },
        yAxis: {
          title: {
            text: 'Number of messages'
          }
        },
        series: [
          {
            name: 'Mesages',
            data: datesOccurrences,
            color: '#DC143C'
          }
        ]
      }
    },
    formatNumber (num) {
      return num.toString().replace(/(\d)(?=(\d{3})+(?!\d))/g, '$1,')
    }
  }
}
</script>

<style scoped>
header {
  text-align: center;
  margin: -40px 0 60px;
}
h4 {
  font-weight: bold;
  font-size: 50px;
  margin: 0 0 10px
}
.center {
  margin: 0 auto;
}
h3 {
  text-align: left;
  font-size: 24px;
  font-weight: 300;
  border-bottom: 1px solid #efefef;
}
.row {
  padding: 0 0 30px;
  align-items: center;
}
p {
  padding: 0 0 25px
}
.bg-grad {
  height: 100%;
  background-color: rgb(2,0,36);
  background-image: linear-gradient(to top, #209cff 0%, #68e0cf 100%);
}
</style>
