<template>
  <v-main
    id="layout"
    :class="{ 'sm-and-up': $vuetify.breakpoint.smAndUp, 'md-and-up': $vuetify.breakpoint.mdAndUp }"
  >
    <v-container>
      <v-layout wrap>
        <v-flex xs12 md12 order-xs2 order-md1>
          <v-row class="header">
            <v-col cols="12" md="5">
              <v-img :src="logoUrl" :width="logoWidth" max-width="140" />
            </v-col>
            <v-col  cols="12" md="4" class="text-md-right">
              <v-icon left light>mdi-face-agent</v-icon> <span v-html="$t('help.contact')"></span>
            </v-col>
            <v-col cols="12" md="3" class="text-md-right">
              {{ $t('help.hours') }}
            </v-col>
          </v-row>
        </v-flex>
        <v-flex xs12 md5 order-xs1 order-md2></v-flex>
      </v-layout>

      <v-layout wrap>
        <v-flex xs12 md7 order-xs2 order-md1>
          <router-view></router-view>
        </v-flex>
        <v-flex xs12 md5 order-xs1 order-md2>
          <OrderSummary />
          <Help />
        </v-flex>
      </v-layout>

      <v-layout wrap>
        <v-flex xs12 md7 order-xs2 order-md1>
          <v-row justify="center" class="header">
            <Footer />
          </v-row>
        </v-flex>
        <v-flex xs12 md5 order-xs1 order-md2></v-flex>
      </v-layout>
    </v-container>
    <div class="footer">{{ $t('generic.copyright') }}</div>
  </v-main>
</template>

<script>
import OrderSummary from '@/components/summaries/OrderSummary.vue'
import Help from '@/components/partials/Help.vue'
import Footer from '@/components/partials/Footer.vue'

export default {
  name: 'Layout',
  data () {
    return {
      logoUrl: process.env.VUE_APP_LOGO_URL,
      logoWidth: process.env.VUE_APP_LOGO_WIDTH
    }
  },
  components: {
    OrderSummary,
    Help,
    Footer
  },
  metaInfo: {
    titleTemplate: `%s | ${process.env.VUE_APP_BRAND_NAME}`,
    link: [{ rel: 'icon', href: process.env.VUE_APP_FAVICON_URL, once: true }]
  }
}
</script>

<style lang="scss" scoped>
.header {
  padding: 1rem 0 2rem;
}
.footer {
  background-color: #000;
  color: #fff;
  text-align: center;
  font-size: .9rem;
  padding: 1rem;
}
</style>
