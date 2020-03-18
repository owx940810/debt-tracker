<template lang="pug">
  #app(:data-selected="selectedCategory")
    h1 Debt Tracker
    button#add(@click="showOverlay()") +
    nav
      button.lend(@click="select('lend')") Lend
      button.borrowed(@click="select('borrowed')") Borrowed
    #container
      Lend(:tickets="tickets.filter(ticket => ticket.category === 'lend')", @showOverlay="showOverlay")
      Borrowed(:tickets="tickets.filter(ticket => ticket.category === 'borrowed')", @showOverlay="showOverlay")

    #overlay(:class="{active: overlayState}")
      button#close(@click="closeOverlay") ✕
      DebtForm(v-if="overlayState", :tickets="tickets", :selectedCategory="selectedCategory" ,:selectedTicket="selectedTicket" @closeOverlay="closeOverlay")
</template>

<script>
  import Borrowed from './components/Borrowed.vue'
  import Lend from './components/Lend.vue'
  import DebtForm from './components/DebtForm.vue'

  export default {
    name: 'App',
    components: {
      Borrowed, Lend, DebtForm
    },

    data () {
      return {
        selectedCategory: 'lend',
        overlayState: false,
        selectedTicket: null,
        tickets: []
      }
    },

    created () {
      if (window.localStorage.getItem('tickets')) {
        this.tickets = JSON.parse(window.localStorage.getItem('tickets'));
      } else {
        this.tickets = [{
          id: 1,
          name: 'test: lend',
          timestamp: 1584494640589,
          currency: 'MYR',
          amount: 9999,
          category: 'lend'
        },
        {
          id: 2,
          name: 'test: borrowed',
          timestamp: 1584494640589,
          currency: 'SGD',
          amount: 9999,
          category: 'borrowed'
        }]
      }
    },

    mounted () {
    },

    methods: {
      select (type) {
        this.selectedCategory = type
      },

      showOverlay (ticket) {
        this.overlayState = true

        if (ticket) {
          this.selectedTicket = ticket
        }
      },

      closeOverlay () {
        this.overlayState = false
        this.selectedTicket = null
      }
    }
  }
</script>

<style lang="sass">
  @import './sass/mixins'
  @import './sass/variables'

  #app
    padding: 50px
    width: 100%
    height: 100%
    overflow: hidden
    max-width: 400px
    margin: 0 auto

    +mobile
      padding: 20px

    &[data-selected="lend"]
      nav::after
        transform: translateX(0)
      .lend
        color: white
      #container
        transform: translateX(0)
        #lend
          opacity: 1
    &[data-selected="borrowed"]
      nav::after
        transform: translateX(calc(100% + 20px))
      .borrowed
        color: white
      #container
        transform: translateX(-50%)
        #borrowed
          opacity: 1

  h1
    color: white

  nav
    position: relative
    width: 100%
    margin-top: 40px
    display: flex
    flex-flow: row nowrap

    &::after
      content: ""
      position: absolute
      top: 100%
      left: 0
      width: 50%
      height: 4px
      width: calc(50% - 10px)
      border-radius: 2px
      background-color: white
      transition: transform $speed-fast ease-in-out

    button
      flex: 0 0 50%
      border: 0
      color: rgba(255,255,255, 0.3)
      transition: color $speed-fast ease-in-out
      padding: 8px 10px
      font-size: 20px
      background-color: transparent

  #container
    position: relative
    width: 200%
    display: flex
    flex-flow: row nowrap
    transition: transform $speed-fast ease-in-out

    > div
      flex: 0 0 50%
      opacity: 0
      transition: opacity $speed-fast ease-in-out

    .tickets
      padding-top: 40px

      .ticket
        background-color: white
        width: 100%
        border-radius: 2px
        padding: 10px
        color: black
        display: grid
        grid-template-columns: 1fr auto
        grid-template-rows: 1fr auto

        & + .ticket
          margin-top: 10px

        .name
          grid-column: 1 / span 1
        .timestamp
          font-size: 12px
          grid-column: 1 / span 1
          grid-row: 2 /span 1
        .amount
          grid-column: 2 / span 1
          grid-row: 1 / span 2
          justify-self: center
          align-self: center
          font-weight: bold

  #add, #close
    position: absolute
    top: 20px
    right: 20px
    font-size: 32px
    color: white
    line-height: 1
    // font-weight: bold
    background-color: transparent
    border: 0
    display: block
    width: 36px
    height: 36px

  #overlay
    position: fixed
    top: 0
    left: 0
    bottom: 0
    right: 0
    background-color: rgba(0,0,0,0.85)
    opacity: 0
    transform: translate(-100%, -100%)
    transition: opacity $speed-fast ease-in-out, transform 0ms 200ms linear

    &.active
      transform: translate(0,0)
      transition: opacity $speed-fast ease-in-out
      opacity: 1


</style>
