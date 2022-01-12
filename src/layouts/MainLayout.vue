<template>
  <div>
    <q-layout
      style="background: linear-gradient(teal 50%, #0c70e5 100% )"
      class="flex-center shadow-2">
      <q-header
        elevated
        class="bg-teal">
        <q-toolbar>
          <q-btn
            flat
            v-on:click="drawer = !drawer"
            round
            dense
            icon="menu"
          />
          <q-toolbar-title>
            Project Bilal
          </q-toolbar-title>
        </q-toolbar>
      </q-header>
      <q-drawer
        v-model="drawer"
        show-if-above
        :width="190"
        :breakpoint="700"
      >
        <q-list padding class="menu-list">
          <div
            v-for="m in menuItems"
            :key="m">
            <q-item
              :active="activeView === m"
              v-on:click="changeView(m)"
              clickable
              v-ripple
            >
              <q-item-section avatar>
                <q-icon :name="m === 'Athans' ? 'mosque' : m.toLowerCase()"/>
              </q-item-section>
              <q-item-section>
                {{ m }}
              </q-item-section>

            </q-item>
            <q-separator v-if="m === 'Settings'"/>
          </div>

        </q-list>
        <q-chip dark color="teal">Made by Omar and Rafik</q-chip>
      </q-drawer>

      <q-page-container>
        <About v-if="activeView === 'Info'"/>
        <Dashboard v-if="activeView === 'Dashboard'"/>
        <Help v-if="activeView === 'Help'"/>
        <Settings
          v-if="activeView === 'Settings'"
          :speakers="speakers"
          @refresh-devices="getNetworkDevices"
        />
      </q-page-container>
    </q-layout>
  </div>
</template>

<script>
import {api} from 'boot/axios'
import {defineComponent} from 'vue'
import About from "components/About";
import Dashboard from "components/Dashboard";
import Help from "components/Help";
import Settings from "components/Settings";
import {SPEAKERS_URL} from "src/utils/constants";

export default defineComponent({
  name: 'MainLayout',
  components: {
    About,
    Dashboard,
    Help,
    Settings,
  },
  data() {
    return {
      speakers: [],
      drawer: false,
      activeView: 'Dashboard',
      menuItems: ['Dashboard', 'Settings', 'Help', 'Info']
    }
  },
  mounted() {
    this.getNetworkDevices()
  },
  methods: {
    changeView(newView) {
      this.activeView = newView
      this.drawer = false
    },
    getNetworkDevices() {
      this.speakers = []
      api.get(SPEAKERS_URL).then(resp => {
          this.speakers = resp.data.speakers
        }
      )
    },
  }
})
</script>
