<template>
  <q-card round style="background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h6">
        Location
      </div>
        <vue-google-autocomplete
          id="map"
          :placeholder="address === '' ? 'Enter your address' : address"
          v-on:placechanged="getGoogleLocation"
        />
    </q-card-section>
    <q-separator dark/>
    <q-card-section>
      <q-toggle
        v-model="manualEntry"
        label="Manually Enter Latitude and Longitude"
      />
      <q-icon name="info" size="sm">
        <q-popup-proxy>
          <q-banner>
            <template v-slot:avatar>
              <q-icon name="place" color="primary"/>
            </template>
            To find the latitude and longitude to an address manually
            <q-btn no-caps flat dense type="a" size="small" icon-right="launch" target="_blank"
                   href="https://www.latlong.net/convert-address-to-lat-long.html">
              see here
            </q-btn>
          </q-banner>
        </q-popup-proxy>
      </q-icon>
      <div v-if="manualEntry">
        <q-input
          v-model="lat"
          :placeholder="latitude === 0 ? 'Latitude' : latitude"
          clearable
          dark
        />
        <q-input
          v-model="long"
          :placeholder="longitude === 0 ? 'Longitude': longitude "
          clearable
          dark
        />
        <q-btn :disabled="!lat || !long" padding="sm" color="teal" @click="setManualLocation">submit</q-btn>
      </div>
    </q-card-section>
  </q-card>
</template>

<script>

import VueGoogleAutocomplete from "components/VueGoogleAutoComplete"

export default {
  name: 'Location',
  data() {
    return {
      lat: '',
      long: '',
      manualEntry: false
    }
  },
  components: {
    VueGoogleAutocomplete
  },
  props: {
    'address': {
      type: String,
      default: 'Enter your address',
    },
    'latitude': {
      type: Number,
      default: 0
    },
    'longitude': {
      type: Number,
      default: 0
    }
  },
  methods: {
    getGoogleLocation(addressData, placeResultData, id) {
      console.log('this is triggered in get google location')
      const resultAddress = placeResultData.formatted_address
      const resultLat = placeResultData.geometry.location.lat()
      const resultLong = placeResultData.geometry.location.lng()
      this.$emit('set-location', resultAddress, resultLat, resultLong)
    },
    setManualLocation() {
      this.$emit('set-location', '', this.lat, this.long)
    },
  },
}
</script>
