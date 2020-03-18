<template lang="pug">
  #debt-form
    h2 {{ type }}
    form(@submit.prevent="submitTicket")
      .input-wrapper
        .input
          input(type="text" pattern=".{1,}" required placeholder=" " v-model="name")
          label Name
      .input-wrapper
        .input
          input(type="tel" pattern=".{1,}" required placeholder=" " v-model="amount")
          label Amount
      .input-wrapper
        .input
          input(type="text" pattern=".{1,}" required placeholder=" " v-model="currency")
          label Currency

      .input-wrapper
        .input
          select(v-model="category")
            option(value="lend") Lend
            option(value="borrowed") Borrowed
          label Category

      button Submit
    button.delete(v-if="selectedTicket", @click="deleteTicket") Delete
</template>

<script>
  import moment from "moment"

  export default {
    name: 'Debt-Form',

    props: ['tickets', 'selectedTicket', 'selectedCategory'],

    data () {
      return {
        name: '',
        amount: '',
        currency: 'MYR',
        category: 'lend',
        type: 'Add'
      }
    },

    created () {
        if (this.selectedTicket) {
          this.type = 'Edit'
          this.name = this.selectedTicket.name
          this.amount = this.selectedTicket.amount
          this.currency = this.selectedTicket.currency
          this.category = this.selectedTicket.category
        } else {
          this.category = this.selectedCategory
        }
    },

    methods: {
      submitTicket () {
        if (this.selectedTicket) {
          const index = this.tickets.findIndex(ticket => ticket.id === this.selectedTicket.id)
          this.tickets[index].name = this.name
          this.tickets[index].amount = this.amount
          this.tickets[index].currency = this.currency
          this.tickets[index].category = this.category
          this.tickets[index].timestamp = Date.now()
        } else {
          let id = 1
          if (this.tickets.length) {
            id = this.tickets[this.tickets.length - 1].id + 1
          }
          this.tickets.push({
            id: id,
            name: this.name,
            amount: this.amount,
            currency: this.currency,
            category: this.category,
            timestamp: Date.now()
          })
        }

        window.localStorage.setItem('tickets', JSON.stringify(this.tickets))
        this.$emit('closeOverlay')
      },

      deleteTicket () {
        const index = this.tickets.findIndex(ticket => ticket.id === this.selectedTicket.id)
        this.tickets.splice(index, 1)

        window.localStorage.setItem('tickets', JSON.stringify(this.tickets))
        this.$emit('closeOverlay')
      }
    }
  }
</script>

<style lang="sass" scoped>
  @import '../sass/mixins'
  @import '../sass/variables'

  #debt-form
    position: absolute
    top: 50%
    left: 50%
    transform: translate(-50%, -50%)
    background-color: white
    width: calc(100% - 40px)
    border-radius: 4px
    padding: 20px

    h2
      margin-top: 0
      margin-bottom: 20px

    button
      color: white
      font-size: 14px
      padding: 6px 10px
      border-radius: 2px
      border: 0
      background-color: $blue
      float: right

      &.delete
        float: left
        background-color: #E74C3C

  select
    position: relative
    background-color: white
    z-index: 1
    border-radius: 2px
    border: 1px solid #D5DBDB
    font-size: 14px
    width: 100%
    padding: 12px 10px
    outline: none

  .input-wrapper
    position: relative
    margin-bottom: 12px
    transition: margin 300ms ease-in-out

    .input
      position: relative
      background-color: white
      z-index: 1
      border-radius: 2px
      border: 1px solid #D5DBDB

    input, label, select
      font-size: 14px
      border: 0 solid

    input, select
      position: relative
      width: 100%
      padding: 12px 10px
      border-color: #D5DBDB
      background-color: transparent
      z-index: 1
      outline: none

      &:focus + label
        transform: translateY(-50%) scale(0.7)
        top: 4px
        background-color: white
        transition: all $speed-normal ease-out, background-color $speed-very-fast 250ms ease-out

      &:not(:placeholder-shown) + label
        transform: translateY(-50%) scale(0.7)
        top: 4px
        background-color: white
        transition: all $speed-normal ease-out, background-color $speed-very-fast 200ms ease-out

    label
      position: absolute
      top: 50%
      left: 10px
      border-color: transparent
      color: #D5DBDB
      z-index: 0
      transition: all $speed-normal ease-out, background-color $speed-very-fast ease-out
      transform: translateY(-50%)
      transform-origin: top left
      background-color: transparent
      padding: 3px 5px

</style>
