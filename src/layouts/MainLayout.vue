<template>
  <div>
    <meta name="viewport" content="width=100%, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
    <q-layout
      style="background: linear-gradient(#6eb6b6 50%, #3285e5 100% )">
      <q-header reveal elevated class="bg-teal">
        <q-toolbar>
          <q-avatar icon="mosque"/>
            <q-toolbar-title>
              Project Bilal
            </q-toolbar-title>
        </q-toolbar>

      </q-header>
      <q-page-container class="q-mb-lg">
        <Dashboard
          v-if="activeView === 'Dashboard'"
          :speaker="speaker"
          :base-u-r-l="baseURL"
          @base-url="updateBaseURL"
        />
        <Settings
          v-if="activeView === 'Settings'"
          :athan-options="athanOptions"
          :athan-settings="athanSettings"
          :address="address"
          :latitude="latitude"
          :longitude="longitude"
          :speaker="speaker"
          :speakers="speakers"
          :jurisprudence-setting="jurisprudenceSetting"
          :method-setting="methodSetting"
          @refresh-devices="getNetworkDevices"
          @set-speaker="setSpeaker"
          @set-location="setLocation"
          @set-method="setMethod"
          @set-jurisprudence="setJurisprudence"
          @set-prayer-settings="setPrayerSettings"
          @test-speaker="testSpeaker"
        />
        <About v-if="activeView === 'About'"/>
      </q-page-container>
      <q-footer reveal elevated>
        <q-tabs
          v-model="activeView"
          dense
          class="text-grey-10 bg-transparent"
          active-color="white"
          indicator-color="teal"
          align="justify"
          animated
        >
          <q-tab :ripple="false" name="Dashboard" icon="dashboard" label="Dashboard"/>
          <q-tab :ripple="false" name="Settings" icon="settings" label="Settings"/>
          <q-tab :ripple="false" name="About" icon="info" label="About"/>
        </q-tabs>
      </q-footer>
    </q-layout>
  </div>
</template>

<script>
import {defineComponent} from 'vue'
import {useQuasar} from 'quasar'
import {api} from 'boot/axios'
import About from "components/About";
import Dashboard from "components/Dashboard";
import Settings from "components/Settings";
import {
  ATHANS_URL,
  JURISPRUDENCE_SETTINGS_URL,
  LOCATION_SETTINGS_URL,
  METHOD_SETTINGS_URL,
  SETTINGS_ALL_URL,
  SPEAKER_SETTINGS_URL,
  SPEAKERS_URL, TEST_SOUND_URL
} from "src/utils/constants";

export default defineComponent({
  name: 'MainLayout',
  components: {
    About,
    Dashboard,
    Settings,
  },
  data() {
    return {
      activeView: 'Dashboard',
      athanOptions: {},
      athanSettings: {},
      methodSetting: {},
      jurisprudenceSetting: '',
      location: {},
      address: '',
      latitude: 0,
      longitude: 0,
      prayerTimes: {},
      speaker: {},
      speakers: [],
      baseURL: 'http://localhost:5002'
    }
  },
  mounted() {
    const $q = useQuasar()
    this.getAllSettings()
  },
  methods: {
    changeView(newView) {
      this.activeView = newView
      this.drawer = false
    },
    getAllSettings() {
      api.get(SETTINGS_ALL_URL).then(resp => {
        if (resp.data) {
          this.athanOptions = resp.data.audio

          if (resp.data.location) {
            this.address = resp.data.location.address
            this.latitude = resp.data.location.lat
            this.longitude = resp.data.location.long
          }
          if (resp.data.calculation) {
            this.jurisprudenceSetting = resp.data.calculation.jurisprudence
            this.methodSetting = resp.data.calculation.method
          }
          this.speaker = resp.data.speaker
          this.configureSettings(resp.data.athans)
        }

        this.getNetworkDevices()
      })
    },
    getNetworkDevices() {
      this.speakers = []
      api.get(SPEAKERS_URL).then(resp => {
          this.speakers = resp.data.speakers
        }
      )
    },
    setSpeaker(speaker) {
      this.speaker = speaker
      api.put(SPEAKER_SETTINGS_URL, speaker).catch(e => {
        console.log(e)
      })
    },
    setLocation(address, lat, long) {
      this.address = address
      this.latitude = lat
      this.longitude = long
      const config = {
        address: address,
        lat: lat,
        long: long
      }
      api.put(LOCATION_SETTINGS_URL, config).catch(e => {
        if (e.response.status === 404) {
          this.$q.notify({
            timeout: 5000,
            type: 'negative',
            message: 'Error saving location from manual input: Please verify your latitude and longitude, and try again.'
          })
        }
      })
    },
    setMethod(method) {
      this.methodSetting = method
      api.put(METHOD_SETTINGS_URL + method)
    },
    setJurisprudence(jurisprudence) {
      this.jurisprudenceSetting = jurisprudence
      api.put(JURISPRUDENCE_SETTINGS_URL + jurisprudence)
    },
    setPrayerSettings(a, prayer, update, isToggle) {
      let url = ATHANS_URL + prayer + '/' + update
      url += isToggle ? `?on=${a}` : '/' + a
      api.put(url)
    },
    loadSettingsPerSalah(prayer, name, icon) {
      const settings = {}
      let data = prayer || {}

      settings['name'] = name
      settings['icon'] = icon
      settings['volume'] = data.volume || 0
      settings['athanToggle'] = !!data.athan_on
      settings['notificationToggle'] = !!data.notification_on
      settings['notificationTime'] = data.notification_time

      if (data && data.audio_id) {
        const athanInfo = this.athanOptions[data.audio_id]
        settings['athan'] = {
          label: athanInfo.name,
          value: athanInfo.audio_id,
          length: athanInfo.length,
          type: athanInfo.type
        }
      }
      if (data && data.notification_id) {
        const notificationInfo = this.athanOptions[data.notification_id]
        settings['notification'] = {
          label: notificationInfo.name,
          value: notificationInfo.audio_id,
          length: notificationInfo.length,
          type: notificationInfo.type
        }
      }
      return settings
    },
    configureSettings(s) {
      let fajr = s ? s.fajr : null
      let dhuhr = s ? s.dhuhr : null
      let asr = s ? s.asr : null
      let maghrib = s ? s.maghrib : null
      let isha = s ? s.isha : null


      const allSettings = {}
      allSettings['fajr'] = this.loadSettingsPerSalah(fajr, 'Fajr', 'mdi-weather-sunset-up')
      allSettings['dhuhr'] = this.loadSettingsPerSalah(dhuhr, 'Dhuhr', 'mdi-weather-sunny')
      allSettings['asr'] = this.loadSettingsPerSalah(asr, 'Asr', 'mdi-weather-partly-cloudy')
      allSettings['maghrib'] = this.loadSettingsPerSalah(maghrib, 'Maghrib', 'mdi-weather-sunset-down')
      allSettings['isha'] = this.loadSettingsPerSalah(isha, 'Isha', 'mdi-weather-night')
      this.athanSettings = allSettings
    },
    updateBaseURL(baseURL){
      this.baseURL = baseURL
      api.defaults.baseURL = baseURL
    },
    testSpeaker(audio_id, speaker) {
      const testSpeaker = speaker || this.speaker

      const config = {
        audio_id: audio_id,
        speaker: testSpeaker
      }
      api.post(TEST_SOUND_URL, config)
    }
  }
})
</script>
