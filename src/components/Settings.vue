<template>
  <q-page class="flex flex-center text-white">
    <div>
      <Location
        class="q-my-sm"
        :address="address"
        :latitude="latitude"
        :longitude="longitude"
        @set-location="setLocation"
      />
      <Calculation
        class="q-my-sm"
        :jurisprudence-setting="jurisprudenceSetting"
        :method-setting="methodSetting"
        @set-method="setMethod"
        @set-jurisprudence="setJurisprudence"
      />
      <Athans
        class="q-my-sm"
        :athan-options="athanOptions"
        :athan-settings="athanSettings"
        @set-prayer-settings="setPrayerSettings"
        @test-speaker="testSpeaker"
      />
      <Speakers
        class="q-my-sm"
        :speakers="speakers"
        :speaker="speaker"
        @refresh-devices="refreshDevices"
        @set-speaker="setSpeaker"
        @test-speaker="testSpeaker"
      />
      <q-card round style="background-color: rgb(0,0,0, 0.25)">
        <q-card-section>
          <q-btn stretch color="red" class="full-width" @click="resetApp">
            RESET APPLICATION
          </q-btn>
        </q-card-section>
      </q-card>
    </div>
  </q-page>
</template>

<script>
import {api} from 'boot/axios'
import Athans from "components/Athans";
import Calculation from "components/Calculation";
import Location from "components/Location";
import Speakers from "components/Speakers";
import {RESET_SETTINGS_URL, TEST_SOUND_URL} from "../utils/constants";

export default {
  name: 'Settings',
  components: {
    Speakers,
    Calculation,
    Location,
    Athans
  },
  props: {
    'athanOptions': {
      type: Object
    },
    'athanSettings': {
      type: Object
    },
    'speakers': {
      type: Array
    },
    'speaker': {
      type: Object
    },
    'address': {
      type: String,
    },
    'latitude': {
      type: Number
    },
    'longitude': {
      type: Number
    },
    'methodSetting': {
      type: String
    },
    'jurisprudenceSetting': {
      type: String
    },
  },
  methods: {
    resetApp() {
      api.delete(RESET_SETTINGS_URL)
    },
    refreshDevices() {
      this.$emit('refresh-devices')
    },
    setSpeaker(speaker) {
      this.$emit('set-speaker', speaker)
    },
    setLocation(address, lat, long) {
      this.$emit('set-location', address, lat, long)
    },
    setMethod(method) {
      this.$emit('set-method', method)
    },
    setJurisprudence(jurisprudence) {
      this.$emit('set-jurisprudence', jurisprudence)
    },
    setPrayerSettings(a, prayer, update, isToggle) {
      this.$emit('set-prayer-settings', a.value, prayer, update, isToggle)
    },
    testSpeaker(audio_id, speaker) {
      this.$emit('test-speaker', audio_id, speaker)
    }

  }
}
</script>
