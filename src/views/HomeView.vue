<script>
import TheWelcome from '../components/TheWelcome.vue'
export default {
  name: 'HomeView',
  components: {
    TheWelcome
  },
  methods: {
    handleLeadCreated(leadId) {
      console.log('lead', leadId)
      this.$router.push({ path: `/about/:${leadId}` })
    }
  }
}
</script>

<template>
  <main>
    <TheWelcome @leadCreated="handleLeadCreated" />
  </main>
</template>
