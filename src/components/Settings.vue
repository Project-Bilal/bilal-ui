<template>
  <q-page class="flex flex-center text-white">
    <div>
      <Location class="q-my-sm"/>
      <Calculation class="q-my-sm"/>
      <Athans class="q-my-sm"/>
      <Speakers
        class="q-my-sm"
        :speakers="speakers"
        @refresh-devices="refreshDevices"
      />
      <q-card round style="background-color: rgb(0,0,0, 0.25)">
        <q-card-section>
          <q-btn color="red" @click="resetApp">
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
import {RESET_SETTINGS_URL} from "../utils/constants";

export default {
  name: 'Settings',
  components: {
    Speakers,
    Calculation,
    Location,
    Athans
  },
  props: {
    'speakers': {
      type: Array
    }
  },
  methods: {
    resetApp() {
      api.delete(RESET_SETTINGS_URL)
    },
    refreshDevices() {
      this.$emit('refresh-devices')
    }
  }
}
</script>
