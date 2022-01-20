<template>
  <q-card dark round style="width: 100%; background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h5">
        Speakers
      </div>
      <div class="text-h6">
        Selected
      </div>
      <div class="col q-gutter-md">
        <div class="row q-gutter-md justify-between">
          <div class="col-1">
            <q-icon size="sm"
                    color="white"
                    :name="speaker ? (speaker.cast_type === 'Group' ? 'speaker_group' :  'speaker') : 'volume_off'"/>
          </div>
          <div v-if="speaker && speaker.name" class="col-9 text-uppercase">
            {{ `${speaker.name} - ${speaker.model}` }}
          </div>
          <div v-else class="col-9 text-uppercase">
            NONE SELECTED
          </div>
        </div>
      </div>
    </q-card-section>
    <q-separator dark/>
    <q-card-section>
      <div class="text-h6">
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
        <q-btn dark :loading="loadingSpeakers" icon="refresh" size="sm" title="refresh" class="float-right"
               @click="$emit('refresh-devices')"/>
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
          :active-class="speaker && s.name === speaker.name ? 'text-orange' :'text-white'"
        >
          <q-item-section>
            <q-btn :icon="s.cast_type === 'Group' ? 'speaker_group' :  'speaker'" align="left" @click="setSpeaker(s)">
              {{ s.name }}
            </q-btn>
          </q-item-section>
          <q-item-section side>
            <q-btn size="sm" class="text-amber" @click="testSound(s)">Test</q-btn>
          </q-item-section>
        </q-item>
      </q-list>
    </q-card-section>
  </q-card>
</template>


<script>

export default {
  name: 'Speakers',
  props: {
    'speakers': {
      type: Array,
    },
    'speaker': {
      type: Object
    },
    'loadingSpeakers': {
      type: Boolean
    }
  },
  methods: {
    setSpeaker(speaker) {
      this.$emit('set-speaker', speaker)
    },
    testSound(speaker) {
      const chirp = '1jishJEjKVBqMqLhR4uPv8X8hjOKIIvgS'
      this.$emit('test-speaker', chirp, speaker)
    }
  }
}
</script>
