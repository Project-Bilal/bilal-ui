<template>
  <q-card dark round style="width: 100%; background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h5">
        Athans
      </div>
      <div v-for="(k) in settings" :key="k">
        <q-card dark round bordered class="q-my-md" style="background-color: rgb(0, 0, 0, 0.1)">
          <q-toggle
            v-model="k.athanToggle"
            :label="k.name"
            class="text-h6"
            @update:model-value="setPrayerSettings({value:k.athanToggle}, k.name, 'toggle-athan', true)"
          />
          <q-icon class="float-right q-ma-sm" size="sm" :name="k.icon"/>
          <q-slide-transition>
            <div v-if="k.athanToggle" class="q-pa-xs q-mx-xs">
              <q-select
                hide-dropdown-icon
                v-model="k.athan"
                :label="'Select Athan'"
                :options="getAudioList"
                options-dense
                behavior="dialog"
                dark
                @update:model-value="setPrayerSettings(k.athan, k.name, 'athan', false)"
              >
                <template v-slot:option="scope">
                  <q-item v-bind="scope.itemProps">
                    <q-item-section>
                      <q-item-label>Name: {{ scope.opt.label }}</q-item-label>
                      <q-item-label caption>Type: {{ scope.opt.type.toUpperCase() }}
                        <q-media-player
                          type="audio"
                          mobile-mode
                          show-spinner
                          dense
                          dark
                          hide-volume-btn
                          :volume="100"
                          :autoplay="false"
                          :source="'https://drive.google.com/uc?id='+scope.opt.value"
                        />
                      </q-item-label>
                      <q-separator dark/>
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
                  @change="setPrayerSettings({value:k.volume}, k.name, 'volume', false)"
                />
              </q-item>
              <q-separator dark/>
              <q-toggle
                v-model="k.notificationToggle"
                left-label
                color="secondary"
                @update:model-value="setPrayerSettings({value:k.notificationToggle}, k.name, 'toggle-notification', true)"
              >
                <template #default>
                  <q-item-label style="color: white" class="text-subtitle1">
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
                    behavior="dialog"
                    dark
                    @update:model-value="setPrayerSettings(k.notification, k.name.toLowerCase(), 'notification', false)"
                  >
                    <template v-slot:option="scope">
                      <q-item v-bind="scope.itemProps">
                        <q-item-section>
                          <q-item-label>Name: {{ scope.opt.label }}</q-item-label>
                          <q-item-label caption>Type: {{ scope.opt.type.toUpperCase() }}

                            <q-media-player
                              type="audio"
                              mobile-mode
                              show-spinner
                              dense
                              dark
                              hide-volume-btn
                              :volume="100"
                              :autoplay="false"
                              :source="'https://drive.google.com/uc?id='+scope.opt.value"
                            />

                          </q-item-label>
                          <q-separator dark/>
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
                    size="md"
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

export default {
  name: 'Athans',
  props: [
    'athanOptions',
    'athanSettings'
  ],
  methods: {
    setPrayerSettings(a, prayer, update, isToggle) {
      if (prayer && typeof prayer === "string") {
        prayer = prayer.toLowerCase()
        this.$emit('set-prayer-settings', a, prayer, update, isToggle)
      }
    },
    testSpeaker(audio) {
      this.$emit('test-speaker', audio)
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
        {label: '5m', value: 5},
        {label: '10m', value: 10},
        {label: '15m', value: 15},
        {label: '30m', value: 30}
      ]
    },
    settings() {
      return this.athanSettings
    }
  }
}
</script>
