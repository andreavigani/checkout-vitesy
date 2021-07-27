<template>
  <div class="order-help px-lg-8">
    <h2 class="order-help-title">
      {{ $t('help.title') | capitalize }}
    </h2>
    <ul class="order-help-nav">
      <li v-for="(faq, index) in faqs" :key="`l-${index}`">
        <v-dialog v-model="dialog" width="800" >
          <template v-slot:activator="{ on, attrs }">
            <v-link name="faq" v-bind="attrs" v-on="on">{{ faq.title }}</v-link>
          </template>
          <v-card>
            <v-card-title class="text-h5">
              {{ faq.title }}
            </v-card-title>
            <v-card-text>
              <div v-html="faq.content"></div>
            </v-card-text>
          </v-card>
        </v-dialog>
      </li>
    </ul>
    <br>
    <h2 class="order-help-title">
      {{ $t('payment_methods.title') | capitalize }}
    </h2>
    <br>
    <img height="39px" src="https://images.prismic.io/vitesy/bd0e3194-94a7-4c94-89c1-75d83be013d8_payment_methods.png"/>
  </div>
</template>

<script>
export default {
  data () {
    return {
      dialog: false
    }
  },
  computed: {
    faqs () {
      return this.$t('help.faq')
    }
  },
  methods: {},
  mounted () {}
}
</script>

<style lang="scss" scoped>
.order-help {
  padding: 1rem 0;
}
.order-help-nav {
  list-style-type: none;
  padding: 1rem 0;
  li {
    padding: .2rem 0;
  }
  a {
    color: #000;
    text-decoration: underline;
    &:hover {
      color: $PRIMARY_COLOR;
      text-decoration: none;
    }
  }
}
</style>
