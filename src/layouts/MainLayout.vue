<template>
  <div>
    <meta name="viewport"
          content="width=device-width, viewport-fit=cover, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
    <q-layout
      dark
      class="overflow-hidden-y full-height"
      style="background: linear-gradient(#5cecec 50%, #3285e5 100% )"
    >
      <q-pull-to-refresh @refresh="refresh">
        <q-header reveal elevated class="bg-teal">
          <q-toolbar>
            <q-avatar icon="mosque"/>
            <q-toolbar-title>
              Project Bilal
            </q-toolbar-title>
            <q-btn icon="refresh" @click="refresh"/>
          </q-toolbar>
        </q-header>
        <q-page-container class="q-mb-lg">
          <Dashboard
            v-if="activeView === 'Dashboard'"
            :speaker="speaker"
            :prayer-times="prayerTimes"
          />
          <Settings
            v-if="activeView === 'Settings'"
            :athan-options="athanOptions"
            :athan-settings="athanSettings"
            :timezone="timezone"
            :latitude="latitude"
            :longitude="longitude"
            :speaker="speaker"
            :speakers="speakers"
            :jurisprudence="jurisprudence"
            :method="method"
            :loading-speakers="loadingSpeakers"
            :loading-location="loadingLocation"
            @refresh-devices="getNetworkDevices"
            @set-speaker="setSpeaker"
            @set-location="setLocation"
            @set-method="setMethod"
            @set-jurisprudence="setJurisprudence"
            @set-prayer-settings="setPrayerSettings"
            @test-speaker="testSpeaker"
            @get-geo-location="getGeoLocation"
            @reset="resetApp"
            @save-settings="saveSettings"
          />
          <About v-if="activeView === 'About'"/>
        </q-page-container>
      </q-pull-to-refresh>
      <q-footer reveal elevated class="q-pb-sm">
        <q-tabs
          v-model="activeView"
          dense
          switch-indicator
          class="text-grey-10 bg-transparent"
          active-color="white"
          indicator-color="teal"
          align="center"
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
import localStorage from 'local-storage'
import {api} from 'boot/axios'
import {DateTime} from 'luxon'
import PrayTimes from "src/utils/PrayTimes";
import About from "components/About";
import Dashboard from "components/Dashboard";
import Settings from "components/Settings";
import {Athans, ATHAN_SETTINGS_DEFAULT} from "src/utils/athans";
import {RESET_SETTINGS_URL, SETTINGS_ALL_URL} from "src/utils/constants";


const UPDATE_MAP = {
  'notification-time': 'notificationTime',
  'toggle-athan': 'athanToggle',
  'toggle-notification': 'notificationToggle',
  'volume': 'volume'
}

export default defineComponent({
  name: 'MainLayout',
  components: {
    About,
    Dashboard,
    Settings,
  },
  data() {
    return {
      localStorage,
      // Values instantiated every app launch
      activeView: 'Dashboard',
      athanOptions: Athans,
      baseURL: 'http://localhost:5002',
      loadingSpeakers: false,
      loadingLocation: false,
      prayerTimes: {},
      speakers: [],

      // Values brought in by local storage on app launch
      athanSettings: ATHAN_SETTINGS_DEFAULT,
      jurisprudence: {
        label: null,
        value: null
      },
      latitude: null,
      longitude: null,
      offset: null,
      method: {
        label: null,
        value: null
      },
      speaker: {},
      timezone: '',
    }
  },
  watch: {
    athanSettings: {
      deep: true,
      handler: function (newVal) {
        this.localStorage('athanSettings', newVal)
      }
    },
    jurisprudence() {
      this.localStorage('jurisprudence', this.jurisprudence)
    },
    latitude() {
      this.localStorage('latitude', this.latitude)
    },
    longitude() {
      this.localStorage('longitude', this.longitude)
    },
    offset() {
      this.localStorage('offset', this.offset)
    },
    method() {
      this.localStorage('method', this.method)
    },
    speaker() {
      this.localStorage('speaker', this.speaker)
    },
    timezone() {
      this.localStorage('timezone', this.timezone)
    },
  },
  beforeMount() {
    const loadedValues = ['athanSettings', 'jurisprudence', 'latitude', 'longitude', 'method', 'speaker', 'timezone', 'offset']
    loadedValues.forEach(value => {
      if (this.localStorage(value))
        this[value] = localStorage(value)
    })
  },
  mounted() {
    this.getPrayerTimes()
    this.getGeoLocation()
  },
  methods: {
    // get device location
    getGeoLocation() {
      this.loadingLocation = true
      navigator.geolocation.getCurrentPosition(this.geoLocationSuccess, this.geolocationError, {enableHighAccuracy: true});
    },
    geoLocationSuccess(position) {
      this.setLocation(position.coords.latitude, position.coords.longitude)
    },
    geolocationError(error) {
      this.sendNotification('negative', 'code: ' + error.code + '\n' +
        'message: ' + error.message + '\n')
      this.loadingLocation = false
    },
    setLocation(lat, long) {
      this.timezone = DateTime.local().zoneName // Americas/Los_Angeles
      this.offset = DateTime.local().offset / 60 // -8.0
      this.latitude = lat
      this.longitude = long
      this.getPrayerTimes()
      this.loadingLocation = false
    },
    // Get network devices
    getNetworkDevices() {
      const scanRoutes = this.scanRoutes
      const appId = chrome.cast.media.DEFAULT_MEDIA_RECEIVER_APP_ID;
      const apiConfig = new chrome.cast.ApiConfig(
        new chrome.cast.SessionRequest(appId, chrome.cast.Capability.AUDIO_OUT),
        function sessionListener(session) {
        },
        function receiverListener(availability) {
        });
      chrome.cast.initialize(apiConfig, function () {
        scanRoutes()
      }, function (err) {
      })
    },
    scanRoutes() {
      const setDevices = this.setDeviceSpeakers
      chrome.cast.cordova.startRouteScan(
        function (routes) {
          setDevices(routes)
          chrome.cast.cordova.stopRouteScan(
            function () {
            },
            function (error) {
            })
        }, function (err) {
          chrome.cast.cordova.stopRouteScan(
            function () {
            },
            function (error) {
            })
        })
    },
    setDeviceSpeakers(devices) {
      this.speakers = devices
    },
    testSpeaker(speaker) {
      const audioId = 'https://storage.googleapis.com/athans/tweet.mp3'
      const mediaInfo = new chrome.cast.media.MediaInfo(audioId, 'audio/mp3')
      console.log('testing speaker')
      chrome.cast.cordova.selectRoute(speaker.id,
        function successCallback(session) {
          console.log("in select route callback success", session)
          session.loadMedia(new chrome.cast.media.LoadRequest(mediaInfo), function (media) {
            console.log("In load media", media)
            setTimeout(function () {
              session.stop()
            }, 2000);
          }, function (err) {
            console.log(err)
            setTimeout(function () {
              session.stop()
            }, 2000);
          })
        }, function errorCallback(err) {
          console.log("in request route error callback", err)
        }
      )
    },
    // Get Prayer Times
    getPrayerTimes() {
      if (this.method.value && this.jurisprudence.label && this.latitude && this.longitude && this.offset) {
        let pt = new PrayTimes(this.method.value)
        pt.asrFactor(this.jurisprudence.label);
        this.prayerTimes = pt.getTimes(new Date(), [this.latitude, this.longitude], this.offset)
      }
    },

    // Setters
    setMethod(method) {
      this.method = method
      this.getPrayerTimes()
    },
    setJurisprudence(jurisprudence) {
      this.jurisprudence = jurisprudence
      this.getPrayerTimes()
    },
    setPrayerSettings(a, prayer, update) {
      let allPrayerSettings = this.athanSettings
      let prayerSettings = allPrayerSettings[prayer]
      if (update === 'athan') {
        prayerSettings.athan = {
          label: this.athanOptions[a].desc,
          value: this.athanOptions[a].audio_id,
          type: this.athanOptions[a].type
        }
      } else if (update === 'notification') {
        prayerSettings.notification = {
          label: this.athanOptions[a].desc,
          value: this.athanOptions[a].audio_id,
          type: this.athanOptions[a].type
        }
      } else {
        prayerSettings[UPDATE_MAP[update]] = a
      }
      allPrayerSettings[prayer] = prayerSettings
      this.athanSettings = allPrayerSettings
    },
    setSpeaker(speaker) {
      this.speaker = speaker
    },
    updateBaseURL(baseURL) {
      this.baseURL = baseURL
      api.defaults.baseURL = baseURL
    },
    refresh(done) {
      this.getPrayerTimes()
      if (typeof done === 'function') done()
      this.sendNotification('positive', 'Refreshed')
    },
    sendNotification(level, message) {
      this.$q.notify({
        timeout: 5000,
        type: level,
        message: message,
        actions: [
          {
            label: 'Dismiss', color: 'white', handler: () => {
            }
          }
        ]
      })
    },
    resetApp() {
      this.latitude = null
      this.longitude = null
      this.timezone = null
      this.offset = null
      this.method = null
      this.jurisprudence = 'Standard'
      this.prayerTimes = {}
      this.athanSettings = ATHAN_SETTINGS_DEFAULT
      api.delete(RESET_SETTINGS_URL)
    },
    saveSettings() {
      let save = true
      if (!this.method || !this.method.value) {
        save = false
        this.sendNotification('negative', "Method is missing")
      }
      if (!this.jurisprudence || !this.jurisprudence.label) {
        save = false
        this.sendNotification('negative', "Jurisprudence is missing")
      }
      if (!this.timezone) {
        save = false
        this.sendNotification('negative', "Location is missing")
      }
      const athans = this.athanSettings
      const prayerList = ['fajr', 'dhuhr', 'asr', 'maghrib', 'isha']
      let atLeastOneAthanIsOn = false
      prayerList.forEach(a => {
          if (athans[a].athanToggle || athans[a].notificationToggle) {
            atLeastOneAthanIsOn = true
          }
          if (athans[a].athanToggle && !athans[a].athan.value) {
            save = false
            this.sendNotification('negative', `${a} athan is on, but missing athan selection`)
          }
          if (athans[a].notificationToggle && !athans[a].notification.value) {
            save = false
            this.sendNotification('negative', `${a} notification is on, but missing notification selection`)
          }
          if (athans[a].notificationToggle && !athans[a].notificationTime) {
            save = false
            this.sendNotification('negative', `${a} notification is on, but missing notification time`)
          }
        }
      )
      if (!atLeastOneAthanIsOn) {
        save = false
        this.sendNotification('negative', 'Please have at least one athan setting on')
      }
      if (save) {
        const config = {
          user_settings: {
            lat: this.latitude,
            long: this.longitude,
            method: this.method.value,
            jurisprudence: this.jurisprudence.label,
            tz: this.timezone,
            athans: this.athanSettings,
            speaker: this.speaker.name
          }
        }
        api.post(SETTINGS_ALL_URL, config).then(resp => {
            this.sendNotification('positive', 'Settings Saved!')
          }
        ).catch(resp => {
          this.sendNotification('negative', "Could not save settings. Please be sure you are on the same network as the RPI.")
        })
      }


    }
  },
})
</script>
