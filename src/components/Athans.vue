<template>
  <q-card dark round style="background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h6">
        Athans
      </div>
      <div v-for="(k) in athanSettings" :key="k">
        <q-card dark round bordered class="q-my-md" style="background-color: rgb(0,0,0, 0.1)">
          <q-toggle
            v-model="k.athanToggle"
            :label="k.name"
            @update:model-value="setPrayerSettings({value:k.athanToggle}, k.name.toLowerCase(), 'toggle-athan', true)"
          />
          <q-icon class="float-right q-ma-sm" size="sm" :name="k.icon"/>
          <q-slide-transition>
            <div v-if="k.athanToggle" class="q-pa-xs q-mx-xs">
              <q-select
                v-model="k.athan"
                :label="'Select Athan'"
                :options="getAudioList"
                :options-dark="false"
                behavior="dialog"
                dark
                @update:model-value="setPrayerSettings(k.athan, k.name.toLowerCase(), 'athan', false)"
              >
                <template v-slot:option="scope">
                  <q-item v-bind="scope.itemProps">
                    <q-item-section>
                      <q-item-label>{{ scope.opt.label }}</q-item-label>
                      <q-item-label caption>{{ scope.opt.type.toUpperCase() }} | {{ scope.opt.length }}</q-item-label>
                    </q-item-section>
                    <q-item-section side>
                      <q-icon color="blue" name="play_circle_outline" @click.prevent.stop="testSound(scope.opt.value)"/>
                    </q-item-section>
                  </q-item>
                </template>
              </q-select>
              <q-item>
                <q-item-section avatar>
                  <q-icon name="volume_up"/>
                </q-item-section>
                <q-slider
                  switch-label-side
                  dark
                  v-model="k.volume"
                  snap
                  label
                  :label-value="'volume: ' + k.volume"
                  label-color="secondary"
                  :step="1"
                  :min="0"
                  :max="10"
                  @change="setPrayerSettings({value:k.volume}, k.name.toLowerCase(), 'volume', false)"
                />
              </q-item>
              <q-separator dark/>
              <q-toggle
                v-model="k.notificationToggle"
                left-label
                color="secondary"
                @update:model-value="setPrayerSettings({value:k.notificationToggle}, k.name.toLowerCase(), 'toggle-notification', true)"
              >
                <template #default>
                  <q-item-label caption style="color: white">
                    Pre-Athan Notification
                  </q-item-label>
                </template>
              </q-toggle>
              <q-slide-transition>
                <div v-show="k.notificationToggle">
                  <q-select
                    v-model="k.notification"
                    :label="'Select Notification'"
                    :options="getAudioList"
                    :options-dark="false"
                    behavior="dialog"
                    dark
                    dense
                    @update:model-value="setPrayerSettings(k.notification, k.name.toLowerCase(), 'notification', false)"
                  >
                    <template v-slot:option="scope">
                      <q-item v-bind="scope.itemProps">
                        <q-item-section>
                          <q-item-label>{{ scope.opt.label }}</q-item-label>
                          <q-item-label caption>{{ scope.opt.type.toUpperCase() }} | {{
                              scope.opt.length
                            }}
                          </q-item-label>
                        </q-item-section>
                        <q-item-section side>
                          <q-icon color="blue" name="play_circle_outline"
                                  @click.prevent.stop="testSound(scope.opt.value)"/>
                        </q-item-section>
                      </q-item>
                    </template>
                  </q-select>
                  <q-btn-toggle
                    v-show="k.notificationToggle"
                    v-model="k.notificationTime"
                    class="q-mt-sm"
                    dark
                    flat
                    size="sm"
                    spread
                    toggle-color="secondary"
                    :options="getNotificationOptions"
                    @update:model-value="setPrayerSettings({value:k.notificationTime}, k.name.toLowerCase(), 'notification-time', false)"
                  />
                </div>
              </q-slide-transition>
            </div>
          </q-slide-transition>
        </q-card>
      </div>
    </q-card-section>
  </q-card>
</template>

<style>
</style>

<script>
import {api} from 'boot/axios'
import {ATHANS_URL, ATHANS_SETTINGS_URL, PLAY_SOUND_URL} from "../utils/constants";

export default {
  name: 'Athans',

  data() {
    return {
      athanOptions: {},
      athanSettings: {},
    }
  },
  mounted() {
    this.getAthanOptions()
  },
  methods: {
    getAthanOptions() {
      api.get(ATHANS_URL).then(resp => {
        this.athanOptions = resp.data
        this.getAthanSettings()
      })
    },
    getAthanSettings() {
      api.get(ATHANS_SETTINGS_URL).then(resp => {
        this.configureSettings(resp.data)
      })
    },
    setPrayerSettings(a, prayer, update, isToggle) {
      let url = ATHANS_URL + prayer + '/' + update
      url += isToggle ? `?on=${a.value}` : '/' + a.value
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
    testSound(audio) {
      api.post(PLAY_SOUND_URL, {audio_id: audio})
    }
  },
  computed: {
    getAudioList() {
      if (this.athanOptions) {
        return Object.values(this.athanOptions).map(a => {
          return {label: a.name, value: a.audio_id, length: a.length, type: a.type,}
        })
      }
      return []
    },
    getNotificationOptions() {
      return [
        {label: '5 min', value: 5},
        {label: '10 min', value: 10},
        {label: '15 min', value: 15},
        {label: '30 min', value: 30}
      ]
    }
  }
}
</script>
