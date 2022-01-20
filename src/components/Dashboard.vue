<template>
  <div>
    <q-page class="flex flex-center text-white">
      <q-card round style="width: 100%; background-color: rgb(0,0,0, 0.25)">
        <q-card-section>
          <q-banner rounded class="bg-primary" :title="getHijri">
            <div class="text-h6 text-center text-white">
              {{ getHijri() }}
            </div>
          </q-banner>
        </q-card-section>
        <q-separator dark/>
        <q-card-section>
          <div class="col text-white q-gutter-md">
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunset-up"/>
              </div>
              <div class="text-h6" :class="nameCol">
                Fajr
              </div>
              <div :class="timeCol">
                {{ prayerTimes && prayerTimes.fajr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunset"/>
              </div>
              <div class="text-h6" :class="nameCol">
                Sunrise
              </div>
              <div :class="timeCol">
                {{ prayerTimes && prayerTimes.sunrise }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunny"/>
              </div>
              <div class="text-h6" :class="nameCol">
                Dhuhur
              </div>
              <div :class="timeCol">
                {{ prayerTimes && prayerTimes.dhuhr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-partly-cloudy"/>
              </div>
              <div class="text-h6" :class="nameCol">
                Asr
              </div>
              <div :class="timeCol">
                {{ prayerTimes && prayerTimes.asr }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-sunset-down"/>
              </div>
              <div class="text-h6" :class="nameCol">
                Maghrib
              </div>
              <div :class="timeCol">
                {{ prayerTimes && prayerTimes.maghrib }}
              </div>
            </div>
            <div class="row q-gutter-md justify-around text-h6">
              <div class="col-1">
                <q-icon size="md" name="mdi-weather-night"/>
              </div>
              <div class="text-h6" :class="nameCol">
                Isha
              </div>
              <div :class="timeCol">
                {{ prayerTimes && prayerTimes.isha }}
              </div>
            </div>
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
                <q-icon
                  size="sm"
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
      </q-card>
    </q-page>
  </div>
</template>

<style>
</style>

<script>

import {getHijriDate} from "src/utils/hijriDate";

export default {
  name: 'Dashboard',
  props: {
    'speaker': {
      type: Object
    },
    'baseURL': {
      type: String
    },
    'prayerTimes': {
      type: Object
    }
  },
  methods: {
    getHijri() {
      return getHijriDate()
    }
  },
  computed: {
    timeCol() {
      return 'col-4'
    },
    nameCol() {
      return 'col-3'
    }
  }
}
</script>
