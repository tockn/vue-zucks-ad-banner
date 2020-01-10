<template>
  <zucks-ad-banner frame-id="442144" @click="clicked"/>
</template>

<script>
  import ZucksAdBanner from './components/ZucksAdBanner'

export default {
  name: 'App',
  components: {
    'zucks-ad-banner': ZucksAdBanner
  },
  methods: {
    clicked () {
      console.log('clicked')
    }
  }
};
</script>

<style>
</style>
