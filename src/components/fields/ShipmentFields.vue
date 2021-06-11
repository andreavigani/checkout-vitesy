<template>
  <div class="shipment">
    <ShipmentSummary :shipment="shipment" :count="count" :total="total" :editable="true" />
    <v-radio-group :value="shippingMethodId">
      <v-radio
        class="available-shipping-method"
        v-for="shipping_method in sortedAvailableShippingMethods"
        :key="shipping_method.id"
        :label="shippingMethodLabel(shipping_method)"
        :value="shipping_method.id"
        color="primary"
        @change="handleChange(shipping_method)"
      ></v-radio>
    </v-radio-group>
  </div>
</template>

<script>
import _ from 'lodash'
import ShipmentSummary from '@/components/summaries/ShipmentSummary'

import { mapState } from 'vuex'
import { mapFields } from 'vuex-map-fields'
import { stepMixin } from '@/mixins/stepMixin'

export default {
  components: {
    ShipmentSummary
  },
  mixins: [stepMixin],
  props: {
    shipment: {
      type: Object,
      required: true
    },
    count: {
      type: Number,
      required: true
    },
    total: {
      type: Number,
      required: true
    }
  },
  data () {
    return {
      delivery_lead_times: []
    }
  },
  methods: {
    async getDeliveryLeadTimes () {
      const response = await this.$store.dispatch('getDeliveryLeadTimes')
      this.delivery_lead_times = response.data || []
    },
    updateValidations () {
      this.invalid_shipments = !_.isEmpty(
        _.find(this.order.shipments, shipment => {
          return _.isEmpty(shipment.shipping_method)
        })
      )
    },
    shippingMethodLabel (shippingMethod) {
      const deliveryLeadTime = this.delivery_lead_times.find((dlt) => { return dlt.relationships.shipping_method.data.id === shippingMethod.id })
      const formattedDeliveryLeadTime = `${deliveryLeadTime.attributes.min_days}-${deliveryLeadTime.attributes.max_days} ${this.$t('order_summary.days')}`
      return `${shippingMethod.name} - ${shippingMethod.formatted_price_amount_for_shipment} - ${formattedDeliveryLeadTime || ''}`
    },
    handleChange (shippingMethod) {
      let payload = {
        order: this.order,
        shipment: this.shipment,
        shippingMethod: shippingMethod
      }
      this.$store.dispatch('setShipmentShippingMethod', payload).then(() => {
        this.trackDeliveryOption(shippingMethod.name)
        this.updateValidations()
      })
    }
  },
  computed: {
    sortedAvailableShippingMethods () {
      return _.sortBy(this.shipment.available_shipping_methods, [
        'price_amount_for_shipment_cents'
      ])
    },
    shippingMethodId () {
      return this.shipment.shipping_method
        ? this.shipment.shipping_method.id
        : null
    },
    ...mapState(['order']),
    ...mapFields(['validations.invalid_shipments'])
  },
  async mounted () {
    this.updateValidations()
    await this.getDeliveryLeadTimes()
  }
}
</script>

<style lang="scss">
.shipment-header {
  margin-bottom: 0.5rem;
}
</style>
