<template lang="pug">
  #borrowed
    .tickets
      .ticket(v-for="ticket in tickets", @click="selectTicket(ticket)")
        .name {{ ticket.name }}
        .timestamp(v-html="getDateTime(ticket.timestamp)")
        .amount {{ ticket.amount }} {{ ticket.currency }}

</template>

<script>
import moment from "moment"

export default {
  name: 'Borrowed',

  props: ['tickets'],

  mounted () {
  },

  methods: {
    getDateTime (timestamp) {
      return moment(timestamp).format('D/MM/YYYY h:mma')
    },

    selectTicket (ticket) {
      this.$emit('showOverlay', ticket)
    }
  }
}
</script>

<style lang="sass" scoped>

</style>
