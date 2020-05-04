<template>
    <q-page class="q-pa-lg bg-grey-3">
    <section id="main_section" class="q-pt-xl">
      <main id="start">
        <section style="text-align: center" v-if="hasData">
          <header class="">
            <h2>{{ chatData.chatName }}</h2>
          </header>

          <h3>General</h3>
          <div class="row vertical-middle">
            <div class="col-xs-12 col-sm-6">
              <h4>{{ chatData.firstChatDate }}</h4>
              <p>First chat</p>
            </div>
            <div class="col-xs-12 col-sm-6">
              <h4>{{ chatData.lastChatDate }}</h4>
              <p>Last chat</p>
            </div>
          </div>
          <div class="row">
            <div class="col-sm-6 col-lg-3 col-xs-12">
              <h4>{{ chatData.totalMessage }}</h4>
              <p>Messages</p>
            </div>
            <div class="col-sm-6 col-lg-3 col-xs-12">
              <h4>{{ chatData.totalImage }}</h4>
              <p>Pictures</p>
            </div>
            <div class="col-sm-6 col-lg-3 col-xs-12">
              <h4>{{ chatData.totalEmoji }}</h4>
              <p>Emojis</p>
            </div>
            <div class="col-sm-6 col-lg-3 col-xs-12">
              <h4>{{ chatData.totalLink }}</h4>
              <p>Links</p>
            </div>
          </div>

          <h3>User</h3>
          <div class="row">
            <div class="col-xs-12 col-sm-6 col-md-4">
              <h4>{{ chatData.participants.length }}</h4>
              <p>Number of users</p>
            </div>
            <div class="col-xs-12 col-sm-6 col-md-4">
              <h4>{{ chatData.participants[0].name }}</h4>
              <p>
                Is chatty nature <br> with <strong>{{ chatData.participants[0].messages }}</strong> messages
              </p>
            </div>
            <div class="col-xs-12 col-sm-12 col-md-4">
              <h4>{{ formatNumber(Math.floor(chatData.totalMessage / chatData.participants.length)) }}</h4>
              <p>Average message per user</p>
            </div>
          </div>
          <highcharts :options="topMessagesOptions" v-if="this.topMessagesOptions"></highcharts>

          <h3>Emojis</h3>
          <div class="row">
            <div class="col-xs-12 col-sm-6 col-md-3">
              <h4>{{ chatData.emojiOccurrence[0].emoji }}</h4>
              <p>Most used Emoji <br> used <strong>{{ chatData.emojiOccurrence[0].occurrence }}</strong> times</p>
            </div>
            <div class="col-xs-12 col-sm-6 col-md-3">
              <h4>{{ chatData.emojiOccurrence.length }}</h4>
              <p>
                Difference emoji used
              </p>
            </div>
            <div class="col-xs-12 col-sm-12 col-md-6">
              <highcharts :options="topEmojisOptions" v-if="this.topEmojisOptions"></highcharts>
            </div>
          </div>

          <h3>Words</h3>
          <div class="row">
            <div class="col-xs-12 col-sm-12 col-md-4">
              <h4>{{ chatData.wordOccurrence.length }}</h4>
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

Highcharts.setOptions({
  legend: {
    enabled: false
  },
  credits: {
    enabled: false
  }
})
export default {
  created () {
    const chatData = {}
    if (LocalStorage.isEmpty()) {
      this.$router.push('/')
    } else {
      this.chatData = LocalStorage.getItem(chatData)
      console.log(this.chatData)
      this.putData()
      this.hasData = true
      console.log(this.topEmojisOptions)
    }
  },
  data () {   
    return {
      hasData: false,
      data: 'boiiiiiiiiiiiii',
      chatData: {},
      topMessagesOptions: {},
      topEmojisOptions: {},
      topWordsOptions: {}
    }
  },
  methods: {
    putData () {
      // for Messages
      var users = []
      var messages = []
      for (let i = 0; i < this.chatData.participants.length; i++) {
        users.push(this.chatData.participants[i].name)
        messages.push(this.chatData.participants[i].messages)
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
          min: 0,
          title: {
            text: 'Number of messages'
          },
          labels: {
            overflow: 'justify'
          }
        },
        series: [
          {
            name: 'Mesages',
            data: messages
          }
        ]
      }

      // for emojis
      var emojis = []
      for (let i = 0; i < 10; i++) {
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
      var occurrences = []
      for (let i = 0; i < 20; i++) {
        words.push(this.chatData.wordOccurrence[i].word)
        occurrences.push(this.chatData.wordOccurrence[i].occurrence)
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
          min: 0,
          title: {
            text: 'Number of word occurence'
          },
          labels: {
            overflow: 'justify'
          }
        },
        series: [
          {
            name: 'Occurence',
            data: occurrences
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
</style>
