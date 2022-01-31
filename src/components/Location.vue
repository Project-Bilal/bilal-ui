<template>
  <q-card round style="width: 100%; background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h6 q-pb-sm">
        Location
      </div>
      <q-input label-color="white" bg-color="teal-6" dark outlined dense v-if="latitude" disable
               :placeholder="'Latitude '+latitude"/>
      <q-input bg-color="teal-6" outlined dense disable dark v-if="longitude" :placeholder="'Longitude '+longitude"/>

      <q-toggle v-model="toggleManual">
        Set Location Manually
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
      </q-toggle>
      <q-form v-if="toggleManual" class="q-gutter-md  q-mt-md">
        <q-input
          v-model="lat"
          outlined
          hint="Latitude"
          :placeholder="latitude !== 0 ? latitude: null"
          clearable
          dark
        />
        <q-input
          v-model="long"
          outlined
          hint="Longitude"
          :placeholder="longitude !== 0 ? longitude :null"
          clearable
          dark
        />
        <q-btn stretch :disabled="!lat || !long" color="teal" @click="setManualLocation">
          submit
        </q-btn>
      </q-form>


    </q-card-section>
  </q-card>
</template>

<script>

export default {
  name: 'Location',
  data() {
    return {
      lat: '',
      long: '',
      toggleManual: false
    }
  },
  props: {
    'timezone': {
      type: String,
    },
    'latitude': {
      type: [Number, String],
    },
    'longitude': {
      type: [Number, String],
    },
    'loadingLocation': {
      type: Boolean
    }
  },
  methods: {
    setManualLocation() {
      this.$emit('set-location', this.lat, this.long)
      this.lat = ''
      this.long = ''
    },
    getGeoLocation() {
      this.$emit('get-geo-location')
    },
  },
}
</script>
