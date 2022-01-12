<template>
  <q-card round style="background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h6">
        Location
      </div>
      <vue-google-autocomplete
        id="map"
        :placeholder="address"
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
        <q-input dark clearable label="Latitude" v-model="latitude"/>
        <q-input dark clearable label="Longitude" v-model="longitude"/>
        <q-btn padding="sm" color="teal" @click="setManualLocation">submit</q-btn>
      </div>
    </q-card-section>
  </q-card>
</template>

<script>

import {api} from 'boot/axios'
import VueGoogleAutocomplete from "components/./VueGoogleAutoComplete"
import {LOCATION_SETTINGS_URL} from "../utils/constants";

export default {
  name: 'Location',
  data() {
    return {
      address: 'Enter your address',
      latitude: '',
      longitude: '',
      manualEntry: false
    }
  },
  components: {
    VueGoogleAutocomplete
  },
  mounted() {
    this.getLocation()
  },
  methods: {
    getGoogleLocation(addressData, placeResultData, id) {
      this.address = placeResultData.formatted_address
      this.latitude = placeResultData.geometry.location.lat()
      this.longitude = placeResultData.geometry.location.lng()
      const config = {
        address: placeResultData.formatted_address,
        lat: placeResultData.geometry.location.lat(),
        long: placeResultData.geometry.location.lng()
      }
      api.put(LOCATION_SETTINGS_URL, config)

    },
    getLocation() {
      api.get(LOCATION_SETTINGS_URL).then(resp => {
        this.address = resp.data.address
        this.latitude = resp.data.lat
        this.longitude = resp.data.long
      })
    },
    setManualLocation() {
      const config = {
        address: this.address,
        lat: this.latitude,
        long: this.longitude
      }
      api.put(LOCATION_SETTINGS_URL, config)
    }
  }
}
</script>
