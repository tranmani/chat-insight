<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn flat @click="drawer = !drawer" round dense icon="menu" />
        <q-toolbar-title>
          Chat Insight
        </q-toolbar-title>
      </q-toolbar>
    </q-header>

    <q-drawer
        v-model="drawer"
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

            <q-item clickable v-ripple.early href="#" v-scroll-to="'#howto'">
              <q-item-section avatar>
                <q-icon name="how_to_reg" />
              </q-item-section>

              <q-item-section>
                How to
              </q-item-section>
            </q-item>

            <q-item clickable v-ripple.early href="#" v-scroll-to="'#faq'">
              <q-item-section avatar>
                <q-icon name="help_outline" />
              </q-item-section>

              <q-item-section>
                FAQ
              </q-item-section>
            </q-item>
          </q-list>
        </q-scroll-area>

        <q-img class="absolute-top" src="https://cdn.quasar.dev/img/material.png" style="height: 150px">
          <div class="absolute-bottom bg-transparent">
            <q-avatar size="56px" class="q-mb-sm">
              <img src="https://i.imgur.com/BQ4PiAc.jpg">
            </q-avatar>
            <div class="text-weight-bold">Huy Tran</div>
            <div><a href="https://fb.com/tranmani251" target="_blank" class="link">@tranmani</a></div>
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
      drawer: false
    }
  }
}
</script>
