<template>
  <q-card round style="width: 100%; background-color: rgb(0,0,0, 0.25)">
    <q-card-section class="q-pb-xl">
      <div class="text-h6 q-pb-s">
        Location
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

        <q-input
          v-model="lat"
          class="q-mb-md"
          outlined
          hint="Latitude"
          :placeholder="latitude !== 0 && latitude"
          clearable
          dark
          dense
        />
        <q-input
          v-model="long"
          outlined
          hint="Longitude"
          :placeholder="longitude !== 0 && longitude "
          clearable
          dark
          dense
        />
        <q-btn class="float-right" :disabled="!lat || !long" padding="sm" color="teal" @click="setManualLocation">submit</q-btn>
      </div>
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
    }
  },
  props: {
    'address': {
      type: String,
      default: 'Enter your address',
    },
    'latitude': {
      type: [Number, String],
      default: 0
    },
    'longitude': {
      type: [Number, String],
      default: 0
    }
  },
  methods: {
    setManualLocation() {
      this.$emit('set-location', '', this.lat, this.long)
    },
  },
}
</script>
