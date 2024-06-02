<script>
import HelloWorld from '../components/HelloWorld.vue'
export default {
  name: 'AboutView',
  components: {
    HelloWorld
  },
  props: ['leadId']
}
</script>

<template>
  <main>
    <HelloWorld :leadId="leadId" />
  </main>
</template>
