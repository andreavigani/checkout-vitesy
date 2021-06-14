<template>
  <div>
    <span v-if="billing && address.billing_info">
      <br />
      <strong>{{ address.company }}</strong>
      <br />
      {{ address.billing_info }}
    </span>
    <strong>{{ address.first_name }} {{ address.last_name }}</strong>
    <br />
    {{ address.line_1 }}
    <br />
    {{ address.zip_code }} {{ address.city }} ({{ address.state_code }}) {{ countryName }}
    <br />
    {{ address.phone }}
  </div>
</template>

<script>
import _ from 'lodash'
import countries from '@/data/countries'

export default {
  props: {
    address: {
      type: Object,
      required: true
    },
    billing: {
      type: Boolean,
      required: false,
      default: true
    }
  },
  computed: {
    countryName () {
      let country = _.find(countries, {
        code: _.upperCase(this.address.country_code)
      })
      return country.name
    }
  }
}
</script>

<style>
</style>
