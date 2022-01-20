<template>
  <q-page class="text-white">
    <div>
      <Location
        class="q-my-sm"
        :timezone="timezone"
        :latitude="latitude"
        :longitude="longitude"
        :loading-location="loadingLocation"
        @set-location="setLocation"
        @get-geo-location="getGeoLocation"
      />

      <Calculation
        class="q-my-sm"
        :jurisprudence="jurisprudence"
        :method="method"
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
        :loading-speakers="loadingSpeakers"
        @refresh-devices="refreshDevices"
        @set-speaker="setSpeaker"
        @test-speaker="testSpeaker"
      />

      <q-card round style="background-color: rgb(0,0,0, 0.25)">
        <q-card-section>
          <q-space class="q-pb-lg"/>
        </q-card-section>
      </q-card>
      <q-page-sticky position="bottom" :offset="[0, 23]">
        <q-btn  fab icon="save" color="primary" class="full-width" @click="saveSettings">Save Settings</q-btn>
      </q-page-sticky>
    </div>
  </q-page>
</template>

<script>
import Athans from "components/Athans";
import Calculation from "components/Calculation";
import Location from "components/Location";
import Speakers from "components/Speakers";

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
    'timezone': {
      type: String,
    },
    'latitude': {
      type: [Number, String],
    },
    'longitude': {
      type: [Number, String],
    },
    'loadingSpeakers':{
      type: Boolean
    },
    'loadingLocation': {
      type: Boolean
    },
    'method': {
      type: Object
    },
    'jurisprudence': {
      type: Object
    },
  },
  methods: {
    resetApp() {
      this.$emit('reset')
    },
    refreshDevices() {
      this.$emit('refresh-devices')
    },
    setSpeaker(speaker) {
      this.$emit('set-speaker', speaker)
    },
    setLocation(lat, long) {
      this.$emit('set-location', lat, long)
    },
    getGeoLocation() {
      this.$emit('get-geo-location')
    },
    setMethod(method) {
      this.$emit('set-method', method)
    },
    setJurisprudence(jurisprudence) {
      this.$emit('set-jurisprudence', jurisprudence)
    },
    setPrayerSettings(a, prayer, update) {
      this.$emit('set-prayer-settings', a, prayer, update)
    },
    testSpeaker(audio_id, speaker) {
      this.$emit('test-speaker', audio_id, speaker)
    },
    saveSettings(){
      this.$emit('save-settings')
    }
  }
}
</script>
