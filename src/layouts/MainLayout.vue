<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated class="print-hide">
      <q-toolbar>
        <q-btn
          v-if="$store.state.preferences.showComputerLink"
          stretch
          flat
          :label="computerText"
          :href="$store.getters['computer/getComputer'].link"
          type="a"
          target="_blank"
          no-caps
          size="21px"
        >
          <q-tooltip>{{ $gettext('View Computer') }}</q-tooltip>
        </q-btn>
        <q-toolbar-title v-else>{{ computerText }}</q-toolbar-title>

        <q-space />

        <q-btn
          v-if="$store.state.preferences.showApps"
          flat
          round
          icon="apps"
          size="lg"
          class="q-mx-xs"
          :disabled="$router.currentRoute.name === 'apps'"
          @click="$router.push({ name: 'apps' })"
        >
          <q-tooltip>{{ $gettext('Apps') }}</q-tooltip>
        </q-btn>

        <q-btn
          v-if="$store.state.preferences.showDevices"
          flat
          round
          icon="mdi-printer"
          size="lg"
          class="q-mx-xs"
          :disabled="$router.currentRoute.name === 'devices'"
          @click="$router.push({ name: 'devices' })"
        >
          <q-tooltip>{{ $gettext('Devices') }}</q-tooltip>
        </q-btn>

        <q-btn
          v-if="$store.state.preferences.showTags"
          flat
          round
          icon="mdi-tag"
          size="lg"
          class="q-mx-xs"
          :disabled="$router.currentRoute.name === 'tags'"
          @click="$router.push({ name: 'tags' })"
        >
          <q-tooltip>{{ $gettext('Tags') }}</q-tooltip>
        </q-btn>

        <q-btn
          v-if="$store.state.preferences.showDetails"
          flat
          round
          icon="mdi-list-status"
          size="lg"
          class="q-mx-xs"
          :disabled="$router.currentRoute.name === 'details'"
          @click="$router.push({ name: 'details' })"
        >
          <q-tooltip>{{ $gettext('Details') }}</q-tooltip>
        </q-btn>

        <q-btn
          v-if="$store.state.preferences.showInfo"
          flat
          round
          icon="info"
          size="lg"
          class="q-mx-xs"
          :disabled="$router.currentRoute.name === 'info'"
          @click="$router.push({ name: 'info' })"
        >
          <q-tooltip>{{ $gettext('Info') }}</q-tooltip>
        </q-btn>

        <q-btn
          flat
          round
          icon="mdi-cog"
          size="lg"
          class="q-mx-xs"
          :disabled="$router.currentRoute.name === 'preferences'"
          @click="$router.push({ name: 'preferences' })"
        >
          <q-tooltip>{{ $gettext('Preferences') }}</q-tooltip>
        </q-btn>

        <q-btn
          v-if="$store.state.preferences.showHelp"
          flat
          round
          icon="help"
          size="lg"
          class="q-mx-xs"
          type="a"
          target="_blank"
          href="https://fun-with-migasfree.readthedocs.io/es/master/chapter10.html#migasfree-play"
        >
          <q-tooltip>{{ $gettext('Help') }}</q-tooltip>
        </q-btn>
      </q-toolbar>
    </q-header>

    <q-page-container>
      <router-view />

      <q-page-sticky
        class="print-hide"
        position="bottom-right"
        :offset="[18, 18]"
      >
        <q-btn
          ref="sync"
          fab
          icon="mdi-play"
          color="secondary"
          :loading="$store.state.executions.isRunningCommand"
          :disabled="$store.state.executions.isRunningCommand"
          @click="synchronize"
        >
          <q-tooltip>{{ $gettext('Synchronize Computer') }}</q-tooltip>
        </q-btn>
      </q-page-sticky>
    </q-page-container>
  </q-layout>
</template>

<script>
import { setInterval } from 'timers'

const { remote } = require('electron')

export default {
  name: 'MainLayout',
  meta: {
    titleTemplate: (title) => `${title} | Migasfree Play`
  },
  computed: {
    computerText() {
      const computer = this.$store.getters['computer/getComputer']

      return computer.cid
        ? `${computer.name} (CID-${computer.cid})`
        : computer.name
    }
  },
  mounted() {
    if (remote.process.argv.includes('sync')) {
      this.synchronize()
      setInterval(this.synchronize, 24 * 60 * 60 * 1000)
    }
  },
  methods: {
    synchronize() {
      this.$store.dispatch('ui/notifyInfo', this.$gettext('Synchronizing...'))

      if (
        this.$store.state.preferences.showSyncDetails &&
        this.$router.currentRoute.name !== 'details'
      )
        this.$router.push({ name: 'details' })

      this.$store.dispatch('executions/run', {
        cmd: 'migasfree sync',
        text: this.$gettext('Synchronization'),
        icon: 'mdi-sync'
      })
    }
  }
}
</script>
