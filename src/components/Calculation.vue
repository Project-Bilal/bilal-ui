<template>
  <q-card round style="width: 100%; background-color: rgb(0,0,0, 0.25)">
    <q-card-section>
      <div class="text-h6">
        Calculation
      </div>
      <q-select
        dark
        :model-value="method.label"
        :options="getCalcOptions"
        label="Method"
        behavior="dialog"
        @update:model-value="setMethod"
      >
        <template v-slot:option="scope">
          <q-item v-bind="scope.itemProps">
            <q-item-section>
              <q-item-label>{{ scope.opt.label }}</q-item-label>
              <q-item-label caption>
                {{ scope.opt.fajr && 'Fajr: ' + scope.opt.fajr }}
                {{ scope.opt.maghrib ? ' | Maghrib: ' + scope.opt.maghrib : null }}
                {{ scope.opt.isha && ' | Isha: ' + scope.opt.isha }}
              </q-item-label>
            </q-item-section>
          </q-item>
        </template>
      </q-select>
      <q-select
        dark
        :model-value="jurisprudence.label"
        :options="Jurisprudence"
        label="Jurisprudence"
        @update:model-value="setJurisprudence"
      >
        <template v-slot:option="scope">
          <q-item v-bind="scope.itemProps">
            <q-item-section>
              <q-item-label>{{ scope.opt.label }}</q-item-label>
              <q-item-label caption>
                {{ scope.opt.details }}
              </q-item-label>
            </q-item-section>
          </q-item>
        </template>
      </q-select>
    </q-card-section>
  </q-card>

</template>

<script>

import {Methods} from "../utils/methods"
import {Jurisprudence} from "src/utils/jurisprudence";


export default {
  name: 'Calculation',
  data() {
    return {
      Jurisprudence,
    }
  },
  props: {
    'method': {
      type: Object
    },
    'jurisprudence': {
      type: Object
    },
  },
  methods: {
    setMethod(method) {
      this.$emit('set-method', method)
    },
    setJurisprudence(jurisprudence) {
      this.$emit('set-jurisprudence', jurisprudence)
    },
    createObject(a) {
      const r = {
        label: a.name,
        value: a.method
      }
      if (a.params.fajr) {
        if (typeof a.params.fajr === 'string') {
          r['fajr'] = a.params.fajr
        } else {
          r['fajr'] = a.params.fajr + '\u00B0'
        }
      }
      if (a.params.maghrib) {
        if (typeof a.params.maghrib === 'string') {
          if (!a.params.maghrib.startsWith('0'))
            r['maghrib'] = a.params.maghrib
        } else {
          r['maghrib'] = a.params.maghrib + '\u00B0'
        }
      }
      if (a.params.isha) {
        if (typeof a.params.isha === 'string') {
          r['isha'] = a.params.isha
        } else {
          r['isha'] = a.params.isha + '\u00B0'
        }
      }
      if (a.params.midnight) {
        r['midnight'] = a.params.midnight
      }
      return r
    }
  },
  computed: {
    getCalcOptions() {
      return Object.values(Methods).map(a => {
        return this.createObject(a)
      })
    },
    getMethodDisplay() {
      if (Methods[this.method])
        return this.createObject(Methods[this.method]).label
      else return null
    }
  }
}
</script>
