<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn flat @click="sideBar = !sideBar" round dense icon="menu" />
        <q-toolbar-title>
          Chat Insight
        </q-toolbar-title>
        <q-btn style="color: white" flat dense v-ripple.early icon="how_to_reg" class="q-mr-md" to="/" v-scroll-to="'#howto'">How To</q-btn>
        <q-btn style="color: white" flat dense v-ripple.early icon="help_outline" class="q-mr-xl" to="/" v-scroll-to="'#faq'">FAQ</q-btn>
      </q-toolbar>
    </q-header>

    <q-drawer
        v-model="sideBar"
        show-if-above
        :width="200"
        :breakpoint="1000"
      >
        <q-scroll-area style="height: calc(100% - 150px); margin-top: 150px; border-right: 1px solid #ddd">
          <q-list padding>
            <q-item exact v-ripple.early to="/">
              <q-item-section avatar>
                <q-icon name="home" />
              </q-item-section>

              <q-item-section>
                Home
              </q-item-section>
            </q-item>

            <q-item exact clickable v-ripple.early to="/result">
              <q-item-section avatar>
                <q-icon name="done_all" />
              </q-item-section>

              <q-item-section>
                Result
              </q-item-section>
            </q-item>
          </q-list>
        </q-scroll-area>

        <q-img class="absolute-top" src="https://cdn.quasar.dev/img/material.png" style="height: 150px">
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="56px" class="q-mb-sm">
              <img src="https://i.imgur.com/BQ4PiAc.jpg">
            </q-avatar>
            <div>A side project by: <a href="https://fb.com/tranmani251" target="_blank" class="link">@tranmani</a></div>
          </div>
        </q-img>
      </q-drawer>
    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>

export default {
  name: 'MainLayout',

  data () {
    return {
      sideBar: false
    }
  }
}
</script>
