<template>
  <q-page class="home">
    <div class="q-pa-md widget-grid">
      <div class="row sm-small-gutter md-gutter gt-md-large-gutter">
        <div class="col col-xs-12 col-sm-6 col-md-6 col-lg-6 col-xl-6">
          <div class="widget-container">
            <div><div class="col-12 text-h1 text-center">Random number!</div></div>
            <div class="row">
              <div class="col-12 text-h3 text-center" id="rng">
                <animated-number
                  :value="rng"
                  :formatValue="formatToPrice"
                  :duration="1000"
                />
              </div>
            </div>
            <div class="row flex justify-center">
              <q-btn
                round
                color="primary"
                :icon="fasDice"
                size="xl"
                @click="rng = Math.floor(Math.random() * 100)"
              />
            </div>
          </div>
        </div>
        <div class="col col-xs-12 col-sm-6 col-md-6 col-lg-6 col-xl-6">
          <div class="widget-container">

          </div>
        </div>
      </div>
      <div class="row sm-small-gutter md-gutter gt-md-large-gutter">
        <div class="col col-xs-12 col-sm-6 col-md-4 col-lg-4 col-xl-4">
          <div class="widget-container">

          </div>
        </div>
        <div class="col col-xs-12 col-sm-6 col-md-4 col-lg-4 col-xl-4">
          <div class="widget-container">

          </div>
        </div>
        <div class="col col-xs-12 col-sm-6 col-md-4 col-lg-4 col-xl-4">
          <div class="widget-container">

          </div>
        </div>
      </div>
      <div class="row sm-small-gutter md-gutter gt-md-large-gutter">
        <div class="col col-xs-12 col-sm-6 col-md-6 col-lg-6 col-xl-6">
          <div class="widget-container">

          </div>
        </div>
        <div class="col col-xs-12 col-sm-6 col-md-6 col-lg-6 col-xl-6">
          <div class="widget-container">

          </div>
        </div>
      </div>
      <div class="row sm-small-gutter md-gutter gt-md-large-gutter">
        <div class="col col-xs-12 col-sm-6 col-md-4 col-lg-4 col-xl-4">
          <div class="widget-container">

          </div>
        </div>
        <div class="col col-xs-12 col-sm-6 col-md-4 col-lg-4 col-xl-4">
          <div class="widget-container">

          </div>
        </div>
        <div class="col col-xs-12 col-sm-12 col-md-4 col-lg-4 col-xl-4">
          <div class="widget-container">

          </div>
        </div>
      </div>
    </div>
  </q-page>
</template>

<script>
    import {fasDice} from '@quasar/extras/fontawesome-v5';
    import AnimatedNumber from "animated-number-vue";
    import { TimelineLite } from "gsap";

    export default {
        components: {
            AnimatedNumber
        },
        name: 'Home',
        data: function () {
            return {
                rng: 0,
                tweenedNumber: 0,
            };
        },
        methods: {
            formatToPrice(value) {
                return `The number is ${Math.floor(value)}`;
            }
        },
        created() {
            this.fasDice = fasDice;
        }
    }
</script>

<style lang="scss" scoped>
  .home {
    background: url("~assets/238542.jpg") center center;
    color: white;
  }

  .row {
    & + .row {
      margin-top: 15px;
    }
  }

  .col {
    & + .col {
      .widget-container {
        margin-left: 15px;
      }
    }

    .widget-container {
      min-height: 200px;
      padding: 10px 15px;
      background: rgba(255, 255, 255, .4);
      border: 1px solid rgba(86, 61, 124, .2);
    }
  }
</style>
