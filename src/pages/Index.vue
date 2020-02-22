<template>
  <q-page class="home">
    <div class="q-pa-md widget-grid">
      <div class="row sm-small-gutter md-gutter gt-md-large-gutter">
        <div class="col col-xs-12 col-sm-6 col-md-6 col-lg-6 col-xl-6">
          <div class="widget-container">
            <div>
              <div class="col-12 text-h3 text-center">Random number!</div>
            </div>
            <div class="row">
              <div class="col-12 text-h4 text-center" id="rng">
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
                @click="calculateRng"
              />
            </div>
          </div>
        </div>
        <div class="col col-xs-12 col-sm-6 col-md-6 col-lg-6 col-xl-6">
          <div class="widget-container">
            <div class="col-12 text-h2 text-center q-mb-md">Forfeit picker</div>
            <div class="row">
              <div class="col-12 text-h5 text-center" id="forfeits">

                <slide-y-up-transition>
                  <vue-luckywheel
                    v-if="pickedForfeits.length < 3"
                    class="q-mt-md"
                    ref="vueLuckywheel"
                    :default-background="false"
                    :rotate-time="3"
                    :prize-index="pickedForfeit"
                    @get-prize="getPrize"
                    @game-over="gameOver">
                    <vue-luckywheel-item v-for="(prize, index) in forfeitOptions" :key="index">
                      <div :class="['name']">{{prize.name}}</div>
                      <!--                    <div class="level" v-if="prize.level > 0">{{prize.level}}</div>-->
                    </vue-luckywheel-item>
                  </vue-luckywheel>
                </slide-y-up-transition>

                <slide-y-up-transition>
                  <q-list bordered separator class="q-mt-md">
                    <q-item
                      v-for="(prize, index) in pickedForfeits"
                      :class="{'bg-primary': prize.mode === 0, 'bg-light-green': prize.mode === 1, 'bg-red-6': prize.mode === 2}"
                      @click="cycleMode(prize)"
                      :key="index"
                      clickable
                      v-ripple
                    >
                      <q-item-section>
                        {{prize.name}}
                      </q-item-section>
                    </q-item>
                  </q-list>
                </slide-y-up-transition>
                <div class="row flex justify-center q-mt-md">
                  <q-btn
                    v-if="pickedForfeits.length === 3"
                    round
                    color="primary"
                    :icon="fasUndoAlt"
                    size="xl"
                    @click="resetForfeits"
                  />
                </div>
              </div>
            </div>
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
    import Vue from 'vue';
    import {fasDice, fasUndoAlt} from '@quasar/extras/fontawesome-v5';
    import JQuery from 'jquery';
    import AnimatedNumber from "animated-number-vue";
    import VueLuckywheel from 'vue-luckywheel';
    import VueLuckywheelItem from 'vue-luckywheel';
    import 'vue-luckywheel/lib/vue-luckywheel.css';
    import {TimelineLite} from "gsap";
    import {SlideYUpTransition} from 'vue2-transitions';

    Vue.use(VueLuckywheel, VueLuckywheelItem);
    window.$ = JQuery;

    export default {
        components: {
            AnimatedNumber,
            SlideYUpTransition,
        },
        name: 'Home',
        data: function () {
            return {
                rng: 0,
                tweenedNumber: 0,
                pickedForfeit: 0,
                pickedForfeits: [],
                modes: [0, 1, 2],
                forfeitOptions: [
                    {
                        name: 'No Scythe',
                        mode: 0,
                    },
                    {
                        name: 'No Sang',
                        mode: 0,
                    },
                    {
                        name: 'Uncharged scythe',
                        mode: 0,
                    },
                    {
                        name: 'No scythe or Sang',
                        mode: 0,
                    },
                    {
                        name: 'Vanilla Client',
                        mode: 0,
                    },
                    {
                        name: 'Week 1 ToB setup',
                        mode: 0,
                    },
                    {
                        name: 'Pure Gear',
                        mode: 0,
                    },
                    {
                        name: 'No brews, food only',
                        mode: 0,
                    },
                    {
                        name: 'Deflne setup',
                        mode: 0,
                    },
                    {
                        name: 'No specing',
                        mode: 0,
                    },
                    {
                        name: 'No supercombats',
                        mode: 0,
                    },
                    {
                        name: 'No protection prayers',
                        mode: 0,
                    },
                    {
                        name: 'No Piety/Rigour/Augruy',
                        mode: 0,
                    },
                    {
                        name: 'No Barrages',
                        mode: 0,
                    },
                    {
                        name: 'Normal Spellbook',
                        mode: 0,
                    },
                    {
                        name: 'No running (20% verzik exempt)',
                        mode: 0,
                    },
                    {
                        name: 'Free pass',
                        mode: 0,
                    },
                    {
                        name: 'Pk Gear',
                        mode: 0,
                    },
                ],
            };
        },
        methods: {
            formatToPrice(value) {
                return `The number is ${Math.floor(value)}`;
            },
            calculateRng() {
                this.rng = Math.floor(Math.random() * 100);
            },
            resetForfeits() {
                this.pickedForfeits = [];
            },
            getPrize() {
                setTimeout(() => {
                    this.prizeIndex = Math.floor(Math.random() * this.forfeitOptions.length);
                    this.$nextTick(this.$refs.vueLuckywheel.draw);
                }, 300);
            },
            gameOver() {
                this.pickedForfeits.push(this.forfeitOptions[this.prizeIndex]);
            },
            cycleMode(prize) {
                switch (prize.mode) {
                    case 0:
                    case 1:
                        prize.mode++;
                        break;
                    case 2:
                        prize.mode = 0;
                        break;
                }
            }
        },
        created() {
            this.fasDice = fasDice;
            this.fasUndoAlt = fasUndoAlt;
        }
    }
</script>

<style lang="scss" scoped>
  .home {
    background: url("~assets/238542.jpg") center center;

    .text-h1 {
      color: white;
    }

    .text-h2 {
      color: white;
    }

    .text-h3 {
      color: white;
    }

    .text-h4 {
      color: white;
    }
  }

  .row {
    & + .row:not(.q-item) {
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

  .vue-lucky-wheel-main {
    /*background-image: url('../assets/demo_background.png');*/
    background-size: cover;
  }

  .vue-lucky-wheel-item-content {
    font-size: 12px;

    .name, .level {
      position: absolute;
      left: 0;
      width: 100%;
    }

    .name {
      top: 40px;
      line-height: 1rem;

      &.thank-you {
        white-space: pre-line;
      }
    }

    .level {
      bottom: 60px;
    }
  }
</style>

<style lang="scss">
  .vue-lucky-wheel {
    width: 450px !important;
    height: 450px !important;

    .vue-lucky-wheel-main {
      width: 450px !important;
      height: 450px !important;

      .vue-lucky-wheel-item {
        width: 225px !important;
        height: 225px !important;

        .vue-lucky-wheel-item-background {
          width: 225px !important;
          height: 225px !important;
        }

        &:nth-child(even) .vue-lucky-wheel-item-background {
          background-color: #f9fbff;
        }

        &:nth-child(odd) .vue-lucky-wheel-item-background {
          background-color: #cbcbcb;
        }

        .vue-lucky-wheel-item-content {
          width: 104px !important;
          height: 225px !important;

          .name {
            font-size: .9rem;
            transform: rotate(90deg);
          }
        }
      }
    }

    .vue-lucky-wheel-button {
      img {
        content: url("~assets/Starkarrow.svg");
        cursor: pointer;
        transition: all .2s ease-in-out;

        &:hover {
          transform: scale(1.1);
        }
      }
    }
  }
</style>
