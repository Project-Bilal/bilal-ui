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
              <div class="col-5 text-h6">
                Fajr
              </div>
              <div class="col-3">
                {{ prayerTimes.fajr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunset"/>
              </div>
              <div class="col-5 text-h6">
                Sunrise
              </div>
              <div class="col-3">
                {{ prayerTimes.sunrise }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunny"/>
              </div>
              <div class="col-5 text-h6">
                Dhuhur
              </div>
              <div class="col-3">
                {{ prayerTimes.dhuhr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-partly-cloudy"/>
              </div>
              <div class="col-5">
                Asr
              </div>
              <div class="col-3">
                {{ prayerTimes.asr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunset-down"/>
              </div>
              <div class="col-5 text-h6">
                Maghrib
              </div>
              <div class="col-3">
                {{ prayerTimes.maghrib }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-night"/>
              </div>
              <div class="col-5 text-h6">
                Isha
              </div>
              <div class="col-3">
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
                        :name="selected ? selected.cast_type === 'Group' ? 'speaker_group' :  'speaker': 'volume_off'"/>
              </div>
              <div v-if="selected" class="col-9">
                {{ `${selected.name} - ${selected.model}` }}
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
            placeholder="http://localhost:5002/"
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
import {PRAYER_TIMES_URL, SPEAKER_SETTINGS_URL} from "../utils/constants";

export default {
  name: 'Dashboard',
  data() {
    return {
      'prayerTimes': null,
      'athanActive': true,
      'fajrActive': true,
      'selected': '',
      'newBaseURL': ''
    }
  },
  mounted() {
    this.getPrayerTimes()
    this.getSpeaker()
  },
  methods: {
    updateBaseURL() {
      api.defaults.baseURL = this.newBaseURL
    },
    getPrayerTimes() {
      api.get(PRAYER_TIMES_URL).then(resp => {
        this.prayerTimes = resp.data
      }).catch(e => {
        console.log(e)
      })
    },
    getSpeaker() {
      api.get(SPEAKER_SETTINGS_URL).then(resp => {
        this.selected = resp.data
      }).catch(e => {
        console.log(e)
      })
    },
  }
}
</script>
