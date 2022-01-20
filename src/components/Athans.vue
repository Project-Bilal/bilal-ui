<template id="Athans">
  <q-card dark round style="width: 100%; background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h5">
        Athans
      </div>
      <div v-for="(k) in athanSettings" :key="k">
        <q-card dark round bordered class="q-my-md" style="background-color: rgb(0, 0, 0, 0.1)">
          <q-toggle
            :model-value="k.athanToggle"
            :label="k.name"
            class="text-h6"
            @click="setPrayerSettings(!k.athanToggle, k.name, 'toggle-athan')"
          />
          <q-icon class="float-right q-ma-sm" size="sm" :name="k.icon"/>
          <q-slide-transition>
            <div v-if="k.athanToggle" class="q-pa-xs q-mx-xs">
              <q-select
                :model-value="k.athan.label"
                label="Select Athan"
                :options="getAudioList"
                behavior="dialog"
                dark
              >
                <template v-slot:option="scope">
                  <q-expansion-item
                    expand-separator
                    dark
                    :header-style="{backgroundColor: 'teal'}"
                    :default-opened="hasChild(scope, k.athan)"
                    header-class="text-weight-bold"
                    :label="scope.opt.label"
                  >
                    <template v-for="child in scope.opt.children" :key="child.label">
                      <q-separator dark/>
                      <q-item
                        clickable
                        v-close-popup
                        :class=" k.athan.value === child.value ? 'text-teal' : 'text-white' "
                      >
                        <q-item-section>
                          <q-item-label
                            @click="setPrayerSettings(child.value, k.name, 'athan')"
                          >
                            {{ child.desc }}
                          </q-item-label>
                          <q-item-label caption class="text-white">
                            <q-media-player
                              type="audio"
                              mobile-mode
                              show-spinner
                              dense
                              dark
                              hide-volume-btn
                              :volume="100"
                              :autoplay="false"
                              :source="'https://storage.googleapis.com/athans/'+ child.value +'.mp3'"
                            />
                          </q-item-label>
                        </q-item-section>
                      </q-item>
                    </template>

                  </q-expansion-item>
                </template>
              </q-select>
              <q-item>
                <q-item-section avatar>
                  <q-icon name="volume_up"/>
                </q-item-section>
                <q-slider
                  :model-value="k.volume"
                  switch-label-side
                  dark
                  snap
                  :step="1"
                  :min="0"
                  :max="10"
                  @change="(a)=>setPrayerSettings(a,k.name, 'volume')"
                />
              </q-item>
              <q-separator dark/>
              <q-toggle
                :model-value="k.notificationToggle"
                left-label
                color="primary"
                @update:model-value="setPrayerSettings(!k.notificationToggle, k.name, 'toggle-notification')"
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
                    :model-value="k.notification.label"
                    label="Select Notification"
                    :options="getAudioList"
                    behavior="dialog"
                    dark
                  >
                    <template v-slot:option="scope">
                      <q-expansion-item
                        expand-separator
                        dark
                        :header-style="{backgroundColor: 'teal'}"
                        :default-opened="hasChild(scope, k.notification)"
                        header-class="text-weight-bold"
                        :label="scope.opt.label"
                      >
                        <template v-for="child in scope.opt.children" :key="child.label">
                          <q-separator dark/>
                          <q-item
                            clickable
                            v-close-popup
                            :class=" k.notification.value === child.value ? 'text-teal' : 'text-white' "
                          >
                            <q-item-section>
                              <q-item-label
                                @click="setPrayerSettings(child.value, k.name, 'notification');"
                              >
                                {{ child.desc }}
                              </q-item-label>
                              <q-item-label caption class="text-white">
                                <q-media-player
                                  type="audio"
                                  mobile-mode
                                  show-spinner
                                  dense
                                  dark
                                  hide-volume-btn
                                  :volume="100"
                                  :autoplay="false"
                                  :source="'https://storage.googleapis.com/athans/'+ child.value +'.mp3'"
                                />
                              </q-item-label>
                            </q-item-section>
                          </q-item>
                        </template>
                      </q-expansion-item>
                    </template>
                  </q-select>
                  <q-btn-group dark rounded flat class="q-mt-sm" push spread>
                    <q-btn
                      flat
                      color="white"
                      :class="k.notificationTime === 5 ? 'text-teal-4': 'text-white'"
                      @click="setPrayerSettings(5, k.name.toLowerCase(), 'notification-time')"
                    >
                      5 min

                    </q-btn>
                    <q-btn
                      dark
                      flat
                      color="white"
                      :class="k.notificationTime === 10 ? 'text-teal-4': 'text-white'"
                      @click="setPrayerSettings(10, k.name.toLowerCase(), 'notification-time')"
                    >
                      10 min
                    </q-btn>
                    <q-btn
                      flat
                      color="white"
                      :class="k.notificationTime === 15 ? 'text-teal-4': 'text-white'"
                      @click="setPrayerSettings(15, k.name.toLowerCase(), 'notification-time')"
                    >
                      15 min
                    </q-btn>
                    <q-btn
                      flat
                      color="white"
                      :class="k.notificationTime === 30 ? 'text-teal-4': 'text-white'"
                      @click="setPrayerSettings(30, k.name.toLowerCase(), 'notification-time')"
                    >
                      30 Min
                    </q-btn>
                  </q-btn-group>
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
    setPrayerSettings(a, prayer, update) {
      if (prayer && typeof prayer === "string") {
        prayer = prayer.toLowerCase()
        this.$emit('set-prayer-settings', a, prayer, update)
      }
    },
    testSpeaker(audio) {
      this.$emit('test-speaker', audio)
    },
    getLabel(scope) {
      return scope.label
    },
    hasChild(scope, athan) {
      return scope.opt.children.some(c => c.value === athan.value)
    }
  },
  computed: {
    getNotificationOptions() {
      return [
        {label: '5m', value: 5},
        {label: '10m', value: 10},
        {label: '15m', value: 15},
        {label: '30m', value: 30}
      ]
    },
    getAudioList() {
      const options = [
        {label: 'Fajr Athans', children: []},
        {label: 'Athans', children: []},
        {label: 'Notifications', children: []},
        {label: 'Takbirat and Duah', children: []}
      ]

      Object.values(this.athanOptions).map(a => {
        if (a.type === 'fajr')
          options[0].children.push({label: a.name, desc: a.desc, value: a.audio_id, length: a.length, type: a.type})
        if (a.type === 'athan')
          options[1].children.push({label: a.name, desc: a.desc, value: a.audio_id, length: a.length, type: a.type})
        if (a.type === 'notification')
          options[2].children.push({label: a.name, desc: a.desc, value: a.audio_id, length: a.length, type: a.type})
        if (a.type === 'takbir')
          options[3].children.push({label: a.name, desc: a.desc, value: a.audio_id, length: a.length, type: a.type})
      })
      return options
    }
  }
}
</script>
