<template>
  <q-layout view="lHh Lpr lFf">
    <q-header elevated>
      <q-toolbar>
        <q-btn
          flat
          dense
          round
          @click="leftDrawerOpen = !leftDrawerOpen"
          icon="menu"
          aria-label="Menu"
        />

        <q-toolbar-title>
          Taran App
        </q-toolbar-title>

        <div>Taran App v0.0.1</div>
      </q-toolbar>
    </q-header>

    <q-drawer
      v-model="leftDrawerOpen"
      bordered
      content-class="bg-grey-1"
    >
      <q-list>
        <q-item-label header class="text-grey-8">Essential Links</q-item-label>
        <EssentialLink
          v-for="link in essentialLinks"
          :key="link.title"
          v-bind="link"
        />
      </q-list>
    </q-drawer>

    <q-page-container>
      <router-view />
    </q-page-container>
  </q-layout>
</template>

<script>
import EssentialLink from 'components/EssentialLink'

export default {
  name: 'MainLayout',

  components: {
    EssentialLink
  },

  data () {
    return {
      leftDrawerOpen: false,
      essentialLinks: [
        {
          title: 'Twitch',
          caption: 'MLGTaran',
          icon: 'pages',
          link: 'https://twitch.tv/MLGTaran'
        },
        {
          title: 'Discord',
          caption: 'Taran\'s Watch',
          icon: 'pages',
          link: 'https://discord.gg/G6wfKkG'
        },
        {
          title: 'Twitter',
          caption: 'MLGTaran',
          icon: 'pages',
          link: 'https://twitter.com/mlgtaran?lang=en'
        },
      ]
    }
  }
}
</script>
