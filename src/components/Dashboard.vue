<template>
  <div>
    <q-page class="flex flex-center text-white">
      <q-card round style="width: 100%; background-color: rgb(0,0,0, 0.25)">
        <q-card-section>
          <div v-if="prayerTimes" class="col text-white q-gutter-md">
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunset-up"/>
              </div>
              <div class="text-h6"
                   :class="nameCol"
              >
                Fajr
              </div>
              <div :class="timeCol">
                {{ prayerTimes.fajr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunset"/>
              </div>
              <div class="text-h6"
                   :class="nameCol"
              >
                Sunrise
              </div>
              <div :class="timeCol">
                {{ prayerTimes.sunrise }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunny"/>
              </div>
              <div class="text-h6"
                   :class="nameCol"
              >
                Dhuhur
              </div>
              <div :class="timeCol">
                {{ prayerTimes.dhuhr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-partly-cloudy"/>
              </div>
              <div class="text-h6"
                   :class="nameCol"
              >
                Asr
              </div>
              <div :class="timeCol">
                {{ prayerTimes.asr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunset-down"/>
              </div>
              <div class="text-h6"
                   :class="nameCol"
              >
                Maghrib
              </div>
              <div :class="timeCol">
                {{ prayerTimes.maghrib }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-night"/>
              </div>
              <div class="text-h6"
                   :class="nameCol"
              >
                Isha
              </div>
              <div :class="timeCol">
                {{ prayerTimes.isha }}
              </div>
            </div>
          </div>
          <div v-else class="text-subtitle1">
            To show prayer times, please configure your settings in the settings menu
          </div>
        </q-card-section>
        <q-separator dark/>
        <q-card-section>
          <div class="text-h6">
            Athan Speaker
          </div>
          <div class="col q-gutter-md">
            <div class="row q-gutter-md justify-around">
              <div class="col-1">
                <q-icon size="sm"
                        :name="speaker ? speaker.cast_type === 'Group' ? 'speaker_group' :  'speaker': 'volume_off'"/>
              </div>
              <div v-if="speaker" class="col-9">
                {{ `${speaker.name} - ${speaker.model}` }}
              </div>
              <div v-else>
                NONE SELECTED
              </div>
            </div>
          </div>
        </q-card-section>
        <q-card-section>
          <q-input
            v-model="newBaseURL"
            label="New Base URL"
            label-color="orange"
            dark
            hint="add the trailing slash, and click out of this text box to save"
            :placeholder="baseURL"
            @update:model-value="updateBaseURL"
          />
        </q-card-section>
      </q-card>
    </q-page>
  </div>
</template>

<style>
</style>

<script>
import {api} from 'boot/axios'
import {PRAYER_TIMES_URL} from "../utils/constants";

export default {
  name: 'Dashboard',
  data() {
    return {
      'prayerTimes': null,
      'newBaseURL': ''
    }
  },
  props: {
    'speaker': {
      type: Object
    },
    'baseURL': {
      type: String
    }
  },
  mounted() {
    this.getPrayerTimes()
  },
  methods: {
    updateBaseURL() {
      this.$emit('base-url', this.newBaseURL)
    },
    getPrayerTimes() {
      api.get(PRAYER_TIMES_URL).then(resp => {
        this.prayerTimes = resp.data
      })
    },
  },
  computed: {
    timeCol() {
      return 'col-4'
    },
    nameCol() {
      return 'col-3'
    },
  }
}
</script>
