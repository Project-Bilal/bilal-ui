<template>
  <q-card dark round style="background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h6">
        Speakers
      </div>
      <div class="text-subtitle1">
        Selected
      </div>
      <div class="col q-gutter-md">
        <div class="row q-gutter-md justify-between">
          <div class="col-1">
            <q-icon size="sm"
                    color="white"
                    :name="selected ? selected.cast_type === 'Group' ? 'speaker_group' :  'speaker' : 'volume_off'"/>
          </div>
          <div v-if="selected.name" class="col-9 text-uppercase">
            {{ `${selected.name} - ${selected.model}` }}
          </div>
          <div v-else class="col-9 text-uppercase">
            NONE SELECTED
          </div>
        </div>
      </div>
    </q-card-section>
    <q-separator dark/>
    <q-card-section>
      <div class="text-subtitle1">
        Available
        <q-icon name="info">
          <q-popup-proxy>
            <q-banner>
              <template v-slot:avatar>
                <q-icon name="speaker_group" color="primary"/>
              </template>
              To play athan on more than one speaker, create a group of speakers in your google home app, and then
              select that group from the list.
              <q-btn flat dense type="a" size="small" icon-right="launch" target="_blank"
                     href="https://support.google.com/assistant/answer/9210727">
                More information
              </q-btn>
            </q-banner>
          </q-popup-proxy>
        </q-icon>
        <q-icon title="refresh" class="float-right" size="sm" name="refresh" @click="$emit('refresh-devices')"/>
      </div>
      <q-banner v-if="speakers.length === 0" class="bg-transparent text-amber">
        <template v-slot:avatar>
          <q-spinner-radio color="primary" size="2em"/>
        </template>
        Searching for devices...
      </q-banner>
      <q-list dark separator v-else class="q-my-xs">
        <q-item
          v-for="s in speakers"
          :key="s.name"
          active
          :active-class="s.name === selected.name ? 'text-orange' :'text-white'"
        >
          <q-item-section avatar>
            <q-icon
              sizze="lg"
              color="white"
              :name="s.cast_type === 'Group' ? 'speaker_group' :  'speaker'"
            />
          </q-item-section>
          <q-item-section>
            <q-btn flat dense align="left" @click="setSpeaker(s)">
              {{ s.name }}
            </q-btn>

          </q-item-section>
          <q-item-section side>
            <q-btn dense flat size="sm" class="text-amber" @click="testSound(s)">Test</q-btn>
          </q-item-section>
        </q-item>
      </q-list>
    </q-card-section>
  </q-card>
</template>


<script>
import {api} from 'boot/axios'
import {SPEAKER_SETTINGS_URL, TEST_SOUND_URL} from "../utils/constants";

export default {
  name: 'Speakers',

  data() {
    return {
      selected: '',
    }
  },
  props: {
    'speakers': {
      type: Array,
    }
  },
  mounted() {
    this.getSpeaker()
  },
  methods: {
    getSpeaker() {
      api.get(SPEAKER_SETTINGS_URL).then(resp => {
        this.selected = resp.data
      }).catch(e => {
        console.log(e)
      })
    },
    setSpeaker(speaker) {
      this.selected = speaker
      api.put(SPEAKER_SETTINGS_URL, speaker).catch(e => {
        console.log(e)
      })
    },
    testSound(speaker) {
      console.log(speaker)
      const config = {
        audio_id: '1jishJEjKVBqMqLhR4uPv8X8hjOKIIvgS',
        speaker: speaker
      }
      api.post(TEST_SOUND_URL, config).then(resp => {
        console.log(resp)
      }).catch(e => {
        console.log(e)
      })
    }
  }
}
</script>
