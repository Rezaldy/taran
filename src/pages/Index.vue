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
              <div class="col-6 q-pa-md">
                <q-input
                  label="Bottom border"
                  v-model.number="rng.min"
                  @change="rngChanged"
                  type="number"
                  :min="1"
                  placeholder="Enter bottom border"
                  bg-color="white"
                  class="q-ma-sm text-white text-center q-pa-md"
                />
              </div>
              <div class="col-6 q-pa-md">
                <q-input
                  label="Top border"
                  v-model.number="rng.max"
                  @change="rngChanged"
                  type="number"
                  :min="rng.min ? rng.min : 0"
                  placeholder="Enter top border"
                  bg-color="white"
                  class="q-ma-sm text-white text-center q-pa-md"
                />
              </div>
            </div>
            <div class="row">
              <div class="col-12">
                <div class="col-12 text-h4 text-center">Quick set borders</div>
              </div>
              <div class="col-12 q-mb-md text-center">
                <q-btn @click="setRngBorders(1,60)" unelevated rounded color="primary" class="q-ma-sm" label="1 - 60"/>
                <q-btn @click="setRngBorders(1,75)" unelevated rounded color="primary" class="q-ma-sm" label="1 - 75"/>
              </div>
            </div>
            <div class="row">
              <div class="col-12 text-h4 text-center" id="rng">
                <animated-number
                  :value="rng.value"
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
              >
                <q-tooltip
                  anchor="top middle"
                  transition-show="flip-right"
                  transition-hide="flip-left"
                >
                  Roll the dice!
                </q-tooltip>
              </q-btn>
            </div>
          </div>
        </div>
        <div class="col col-xs-12 col-sm-6 col-md-6 col-lg-6 col-xl-6">
          <div class="widget-container">
            <div class="row">
              <forfeit-picker></forfeit-picker>
            </div>
          </div>
        </div>
      </div>
      <div class="row sm-small-gutter md-gutter gt-md-large-gutter">
        <div class="col col-xs-12 col-sm-6 col-md-4 col-lg-4 col-xl-4">
          <div class="widget-container">
            <div class="col-12 text-h2 text-center q-mb-md">Lootlist picker</div>
            <q-dialog v-model="lootCallWinnerDialog">
              <q-card>
                <q-card-section class="row items-center q-pb-none">
                  <div class="text-h6">THE WINNER IS...</div>
                  <q-space/>
                  <q-btn icon="close" flat round dense v-close-popup/>
                </q-card-section>

                <q-card-section>
                  <h4 class="text-center">{{ lootCallWinner }}!!!</h4>
                  <h6>Congratulations, I hope you're following.. And that you know where ToB is.</h6>
                </q-card-section>
              </q-card>
            </q-dialog>
            <q-list bordered separator>
              <q-input
                v-if="lootListOptions.length"
                label="List filter"
                v-model="lootFilter"
                :min="1"
                placeholder="Enter loot name"
                bg-color="white"
                class="q-ma-sm text-white text-center q-pa-md"
              >
                <template v-slot:append>
                  <q-icon name="search"/>
                </template>
              </q-input>
              <q-item
                :class="{'bg-red-10': looter.active === false, 'bg-green-9': looter.active === true}"
                clickable
                v-ripple
                v-for="(looter, index) in getFilteredLootList(lootListOptions)"
                :key="index"
                :active="looter.active"
                @click="looter.active = !looter.active"
              >
                <q-item-section avatar>
                  <q-icon v-if="looter.active" name="fas fa-check"/>
                  <q-icon v-else name="fas fa-times"/>
                </q-item-section>
                <q-item-section>
                  {{looter.name}}
                </q-item-section>
                <q-item-section>
                  {{looter.call}}
                </q-item-section>
              </q-item>
            </q-list>
            <div class="row flex justify-evenly q-mt-md q-mb-md">
              <q-btn
                round
                v-if="lootListOptions.length"
                color="primary"
                :icon="fasDice"
                ref="lootInput"
                size="sm"
                @click="pickLootCaller"
              >
                <q-tooltip
                  anchor="top middle"
                  transition-show="flip-right"
                  transition-hide="flip-left"
                >
                  Pick someone!
                </q-tooltip>
              </q-btn>

              <q-btn
                push
                color="primary"
                :icon="fasPlus"
                v-if="lootListOptions.length"
                @click="lootCallToAdd = {}"
              >
                <q-tooltip
                  anchor="top middle"
                  transition-show="flip-right"
                  transition-hide="flip-left"
                >
                  Add lootcaller
                </q-tooltip>
                <q-popup-proxy transition-show="scale" transition-hide="scale">
                  <q-input
                    label="Name"
                    v-model="lootCallToAdd.name"
                    placeholder="Enter viewer name"
                    ref="addLootCallName"
                    class="q-ma-lg"
                  />
                  <q-input
                    label="Loot call"
                    v-model="lootCallToAdd.call"
                    placeholder="Enter lootcall"
                    ref="addLootCallCall"
                    class="q-ma-lg"
                  />
                  <div class="row flex justify-center q-mt-sm q-mb-sm">
                    <q-btn
                      push
                      color="primary"
                      :icon="fasPlus"
                      @click="addLootCall"
                    />
                  </div>
                </q-popup-proxy>
              </q-btn>
              <q-btn
                round
                v-if="lootListOptions.length"
                color="primary"
                :icon="fasUndoAlt"
                ref="lootInput"
                size="sm"
                @click="lootListOptions = []"
              >
                <q-tooltip
                  anchor="top middle"
                  transition-show="flip-right"
                  transition-hide="flip-left"
                >
                  Reset
                </q-tooltip>
              </q-btn>
            </div>
            <div class="q-pa-md">
              <q-input
                v-if="!lootListOptions.length"
                v-model="lootInput"
                label="Loot list text"
                bg-color="white"
                placeholder="Paste loot list here"
                text-color="white"
                class="text-white"
                filled
                autogrow/>
            </div>
            <div class="row flex justify-evenly q-mt-md">
              <q-btn
                v-if="!lootListOptions.length"
                round
                color="primary"
                :icon="fasSearch"
                ref="lootInput"
                size="xl"
                @click="processLootInput"
              >
                <q-tooltip
                  anchor="top middle"
                  transition-show="flip-right"
                  transition-hide="flip-left"
                >
                  Process list
                </q-tooltip>
              </q-btn>
            </div>

            <div class="row flex justify-evenly q-mt-md">
              <q-btn
                round
                color="secondary"
                :icon="fasList"
                size="xl"
                @click="openLootList"
              >
                <q-tooltip
                  anchor="top middle"
                  transition-show="flip-right"
                  transition-hide="flip-left"
                >
                  Open loot list
                </q-tooltip>
              </q-btn>
              <q-btn
                round
                color="secondary"
                :icon="fasUndoAlt"
                disabled
                size="xl"
                @click="retrieveLootListOptions"
              >
                <q-tooltip
                  anchor="top middle"
                  transition-show="flip-right"
                  transition-hide="flip-left"
                >
                  Refresh loot list
                </q-tooltip>
              </q-btn>
            </div>
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
    </div>
  </q-page>
</template>

<script>
  import Vue from 'vue';
  import {openURL} from 'quasar';
  import {fasDice, fasUndoAlt, fasList, fasSearch, fasPlus} from '@quasar/extras/fontawesome-v5';
  import AnimatedNumber from 'animated-number-vue';
  import axios from 'axios';
  import {TimelineLite} from 'gsap';
  import ForfeitPicker from '../components/ForfeitPicker';

  export default {
    components: {
      ForfeitPicker,
      AnimatedNumber,
    },
    name: 'Home',
    data: function () {
      return {
        rng: {
          min: 1,
          max: 60,
          value: 0
        },
        tweenedNumber: 0,
        lootListOptions: [],
        lootFilter: '',
        lootInput: '',
        lootCallWinnerDialog: false,
        lootCallWinner: '',
        lootCallToAdd: {
          name: '',
          call: '',
          active: false,
        },
      };
    },
    methods: {
      getFilteredLootList() {
        if (this.lootFilter.length) {
          return this.lootListOptions.filter((option) =>
            option.call.toLowerCase().indexOf(this.lootFilter.toLowerCase()) !== -1);
        }

        return this.lootListOptions;
      },
      formatToPrice(value) {
        return `The number is ${Math.floor(value)}`;
      },
      calculateRng() {
        if (this.rng.min > this.rng.max) {
          this.$q.notify({
            message: 'Can a minimum value truly be higher than a maximum value?',
            caption: 'Idiot.',
            type: 'warning'
          });
        } else if (!Number.isInteger(this.rng.min) || !Number.isInteger(this.rng.max)) {
          this.$q.notify({
            message: 'Actually enter values.',
            caption: 'Idiot.',
            type: 'warning'
          });
        } else {
          this.rng.value = Math.random() * (this.rng.max - this.rng.min) + this.rng.min;
        }
      },
      setRngBorders(min, max) {
        this.rng.min = min;
        this.rng.max = max;
        this.$q.notify({
          message: `Minimum value set to ${min}`,
          caption: 'Value changed.',
          type: 'success'
        });
        this.$q.notify({
          message: `Maximum value set to ${max}`,
          caption: 'Value changed.',
          type: 'success'
        });
      },
      openLootList() {
        openURL('https://twitch.center/customapi/quote/list?token=ba3ed13c');
      },
      retrieveLootListOptions() {
        const testURL = '//twitch.center/customapi/quote/list?token=ba3ed13c';
        const myInit = {
          method: 'GET',
          mode: 'no-cors',
        };

        const myRequest = new Request(testURL, myInit);

        fetch(myRequest).then(function (response) {
          return response;
        }).then(function (response) {
          console.log(response);
        }).catch(function (e) {
          console.log(e);
        });
      },
      processLootInput() {
        // console.log(this.lootInput.split('\n'));
        const lootCallers = [];
        this.lootListOptions = [];

        if (this.lootInput.length) {
          if (this.lootInput === 'There are no quotes added') {
            this.$q.notify({
              message: 'Did you read? The list is empty.',
              caption: 'Idiot.',
              type: 'warning'
            });
          } else {
            // \d*\. (.*)\[(.*)\] Regex for one line
            if (this.lootInput.match(/(?:\d*\. (.*)\[(.*)]\n]?)/gm)) {
              for (const lootCall of this.lootInput.match(/(?:\d*\. (.*)\[(.*)]\n]?)/gm)) {
                const lootCallMatch = lootCall.match(/\d*\. (.*)\[(.*)]/);
                const lootCallObj = {
                  name: lootCallMatch[1],
                  call: decodeURI(lootCallMatch[2].charAt(0).toUpperCase() + lootCallMatch[2].slice(1)),
                  active: false,
                };
                this.lootListOptions.push(lootCallObj);
              }
              this.$q.notify({
                message: 'Loot processed!',
                caption: 'Select the people that chose correctly.',
                type: 'positive'
              });
            } else {
              this.$q.notify({
                message: 'Okay, cool, but I can\'t read that.',
                caption: 'Try again, but better.',
                type: 'negative'
              });
            }
          }
        } else {
          this.$q.notify({
            message: 'WHERE IS THE DAMN LOOTLIST?',
            caption: 'Sit.',
            type: 'warning'
          });
        }
      },
      pickLootCaller() {
        const chosenCalls = this.lootListOptions.filter(function (o) {
          return o.active;
        }).map(o => o.name);
        this.lootCallWinner = chosenCalls[Math.floor(Math.random() * chosenCalls.length)];
        this.lootCallWinnerDialog = true;
      },
      addLootCall() {
        if (this.lootCallToAdd.name !== '' && this.lootCallToAdd.call) {
          this.lootListOptions.push({
            name: this.lootCallToAdd.name,
            call: this.lootCallToAdd.call,
            active: this.lootCallToAdd.active,
          });
          this.lootCallToAdd.name = '';
          this.lootCallToAdd.call = '';
        } else {
          this.$q.notify({
            message: 'Check if you have all fields filled in.',
            type: 'negative'
          });
        }
      },
      rngChanged() {
        console.log(this.rng);
      }
    },
    created() {
      this.fasDice = fasDice;
      this.fasUndoAlt = fasUndoAlt;
      this.fasList = fasList;
      this.fasSearch = fasSearch;
      this.fasPlus = fasPlus;
    }
  };
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

    .q-item {
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
