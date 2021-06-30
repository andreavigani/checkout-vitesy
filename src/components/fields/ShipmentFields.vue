<template>
  <div class="shipment">
    <!-- <ShipmentSummary :shipment="shipment" :count="count" :total="total" :editable="true" /> -->
    <v-radio-group :value="shippingMethodId">
      <template  v-for="shipping_method in sortedAvailableShippingMethods">
        <v-radio
          class="available-shipping-method"
          v-if="!selectedOnly || shippingMethodId === shipping_method.id"
          :key="shipping_method.id"
          :value="shipping_method.id"
          color="primary"
          @change="handleChange(shipping_method)"
        >
          <template slot="label">
            <div class="shipping_method_label">
              <div class="shipping_method_label__content">
                <h3>{{ $t('order_summary.within') | capitalize }} {{ shippingMethodLabel(shipping_method).min_days }} - {{ shippingMethodLabel(shipping_method).max_days }} {{ $t('order_summary.days') }}</h3>
                <v-icon left light>mdi-truck</v-icon>
                {{ $i18n.locale === 'it' ? $t('order_summary.shipping') : '' | capitalize }} {{ shippingMethodLabel(shipping_method).name }} {{ $i18n.locale !== 'it' ? $t('order_summary.shipping') : '' | capitalize }}
              </div>
              <div>{{ shippingMethodLabel(shipping_method).price }}</div>
            </div>
          </template>
        </v-radio>
      </template>
    </v-radio-group>
  </div>
</template>

<script>
import _ from 'lodash'
// import ShipmentSummary from '@/components/summaries/ShipmentSummary'

import { mapState } from 'vuex'
import { mapFields } from 'vuex-map-fields'
import { stepMixin } from '@/mixins/stepMixin'

export default {
  // components: {
  //   ShipmentSummary
  // },
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
    },
    selectedOnly: {
      type: Boolean,
      required: false
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
      // const formattedDeliveryLeadTime = `${deliveryLeadTime.attributes.min_days}-${deliveryLeadTime.attributes.max_days} ${this.$t('order_summary.days')}`
      // return `${shippingMethod.name} - ${shippingMethod.formatted_price_amount_for_shipment} - ${formattedDeliveryLeadTime || ''}`
      return {
        name: shippingMethod.name,
        price: parseInt(shippingMethod.price_amount_for_shipment_cents) > 0 ? shippingMethod.formatted_price_amount_for_shipment : this.$t('generic.free'),
        min_days: deliveryLeadTime.attributes.min_days,
        max_days: deliveryLeadTime.attributes.max_days
      }
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
.shipping_method_label {
  display: inline-flex;
  flex-flow: row wrap;
  width: 100%;
  justify-content: space-between;
  color: #000;
  .shipping_method_label__content {
    margin-left: .5rem;
  }
  h3 {
    margin-bottom: .75rem;
  }
}
</style>
