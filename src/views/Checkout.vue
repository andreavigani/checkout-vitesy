<template>
  <v-stepper v-model="current_step" vertical>
    <CustomerStep :step="1" />
    <DeliveryStep :step="2" v-if="requires_delivery" />
    <PaymentStep :step="paymentStep" v-if="requires_payment" />
  </v-stepper>
</template>

<script>
import CustomerStep from '@/components/steps/CustomerStep'
import DeliveryStep from '@/components/steps/DeliveryStep'
import PaymentStep from '@/components/steps/PaymentStep'
import { mapFields } from 'vuex-map-fields'

export default {
  components: {
    CustomerStep,
    DeliveryStep,
    PaymentStep
  },
  computed: {
    paymentStep () {
      return this.requires_delivery ? 3 : 2
    },
    ...mapFields([
      'current_step',
      'order',
      'requires_delivery',
      'requires_payment'
    ])
  },
  metaInfo () {
    return {
      title: `Checkout #${this.order.number} | Vitesy`
    }
  }
}
</script>

<style lang="scss">
.v-sheet.v-stepper {
  box-shadow: none!important;
  padding: 0 1rem!important;
}
.v-subheader {
  padding: 0;
}

.v-stepper .step-wrapper:first-of-type .v-stepper__step {
  margin-top: 0;
  border-top: none;
}

.step-wrapper {
  .v-stepper__step {
    border-top: 1px solid $v-border;
    margin-top: 2rem;
    border-bottom: 1px solid $v-border;
    padding-left: 0;
    padding-right: 0;
    &__step {
      width: 36px;
      height: 36px;
      font-size: 1rem;
    }
  }
  .v-stepper__label {
    flex-flow: row wrap;
    align-items: center;
    justify-content: space-between;
    width: 100%;
    p, small {
      margin-top: 0.5rem;
      color: rgba(0,0,0,0.5)!important;
    }
  }

  .v-stepper__content {
    border-left: none!important;
    margin: 0!important;
    padding-left: 0!important;
    padding-right: 0!important;
  }

  .v-btn {
    border-radius: 0;
    height: 50px!important;
  }
}

.theme--light {
  &.v-stepper {
    .v-stepper__step--editable {
      &:hover {
        background: none;
      }
    }
  }
}

.v-stepper__step:not(.v-stepper__step--active):not(.v-stepper__step--complete):not(.v-stepper__step--error) .v-stepper__step__step {
  border: 1px solid rgba(0, 0, 0, 0.38);
  color: rgba(0, 0, 0, 0.38);
  background: none!important;
}

.sm-and-up {
  .v-stepper {
    padding: 2rem;
  }

  .step-summary {
    // margin-top: 16px;
    // margin-left: 36px;
    // padding-left: 24px;
    // padding-bottom: 16px;
    // border-left: 1px solid $v-border;
    // border-bottom: 0;
  }

  .step-wrapper {
    .v-stepper__content {
      margin: 0px -36px -16px 36px;
      padding: 16px 60px 16px 23px;
    }
    &:not(:last-child) > .v-stepper__content {
      border-left: 1px solid $v-border;
    }
    button {
      &.v-size--default {
        min-width: 50%;
        margin-bottom: 1rem;
      }
      &#payment-step-submit {
        margin-bottom: 0;
      }
    }
  }
}

.v-radio {
  border: 1px solid rgba(0,0,0,0.1);
  padding: 1rem;
  &:hover, &.v-item--active {
    border: 1px solid $PRIMARY_COLOR;
  }
}
</style>
