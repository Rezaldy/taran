<template>
  <div class="col-12 text-h5 text-center" id="forfeits">
    <div class="col-12 text-h2 text-center text-white q-mb-md">Wheel of Theon</div>
    <vue-luckywheel
      :key="t3enabled"
      v-if="pickedForfeits.length < 3 && !t3enabled"
      class="q-mt-md absolute"
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
    <vue-luckywheel
      :key="t3enabled"
      v-else-if="pickedForfeits.length < 3 && t3enabled"
      class="q-mt-md"
      ref="vueLuckywheel"
      :default-background="false"
      :rotate-time="3"
      :prize-index="pickedForfeit"
      @get-prize="getPrize"
      @game-over="gameOver">
      <vue-luckywheel-item v-for="(prize, index) in [...forfeitOptions, ...t3Options]" :key="index">
        <div :class="['name']">{{prize.name}}</div>
        <!--                    <div class="level" v-if="prize.level > 0">{{prize.level}}</div>-->
      </vue-luckywheel-item>
    </vue-luckywheel>

    <slide-y-up-transition>
      <q-list bordered separator class="q-mt-md">
        <q-item
          v-for="(prize, index) in pickedForfeits"
          class="text-white"
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
    <div class="row flex justify-around q-mt-md">
      <q-toggle
        v-model="t3enabled"
        checked-icon="check"
        color="green"
        label="Tier 3 sub"
        unchecked-icon="clear"
      />
      <q-btn label="All forfeits" color="primary" @click="showOptions = true"/>
      <q-dialog v-model="showOptions">
        <q-card>
          <q-card-section class="row items-center q-pb-none">
            <div class="text-h6">Taran's Wheel of Theon</div>
            <q-space/>
            <q-btn icon="close" flat round dense v-close-popup/>
          </q-card-section>

          <q-card-section>
            <div class="col-12 text-h4 text-center q-mb-md">Default forfeits</div>
            <q-list bordered separator>
              <q-item clickable v-ripple v-for="(option, index) in forfeitOptions" :key="index">
                <q-item-section>
                  <q-item-label overline>{{option.name}}</q-item-label>
                  <q-item-label v-if="option.hasOwnProperty('description')" caption>{{option.description}}
                  </q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </q-card-section>
          <q-separator/>
          <q-card-section>
            <div class="col-12 text-h4 text-center q-mb-md">Tier 3 sub forfeits</div>
            <q-list bordered separator>
              <q-item clickable v-ripple v-for="(option, index) in t3Options" :key="index">
                <q-item-section>
                  <q-item-label overline>{{option.name}}</q-item-label>
                  <q-item-label v-if="option.hasOwnProperty('description')" caption>Item with caption</q-item-label>
                </q-item-section>
              </q-item>
            </q-list>
          </q-card-section>
        </q-card>
      </q-dialog>
    </div>
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
</template>

<script>
  import Vue from 'vue';
  import VueLuckywheel from 'vue-luckywheel';
  import VueLuckywheelItem from 'vue-luckywheel';
  import 'vue-luckywheel/lib/vue-luckywheel.css';
  import {SlideYUpTransition} from 'vue2-transitions';

  Vue.use(VueLuckywheel, VueLuckywheelItem);

  export default {
    // name: 'ForfeitPicker',
    components: {
      SlideYUpTransition,
    },
    data() {
      return {
        showOptions: false,
        t3enabled: false,
        pickedForfeit: 0,
        pickedForfeits: [],
        modes: [0, 1, 2],
        forfeitOptions: [
          {
            name: 'PK Gear',
            mode: 0,
          },
          {
            name: 'No magic',
            mode: 0,
          },
          {
            name: 'No scythe',
            mode: 0,
          },
          {
            name: 'No Scythe or Sang',
            mode: 0,
          },
          {
            name: 'Twisted Bow & Chins',
            mode: 0,
          },
          {
            name: 'Pure gear',
            mode: 0,
          },
          {
            name: 'No Protection prayers',
            mode: 0,
          },
          {
            name: 'Normal spellbook',
            mode: 0,
          },
          {
            name: 'No running',
            description: '20% Verzik Exempt',
            mode: 0,
          },
          {
            name: 'Learner set-up',
            description: 'Full Void, Whip, Trident, Cheese cape, Dragon defender',
            mode: 0,
          },
          {
            name: 'No armour',
            description: 'No helmet, no body armour, no leg armour',
            mode: 0,
          },
          {
            name: 'F2P Armour',
            description: 'Rings exempt',
            mode: 0,
          },
          {
            name: 'Free pass',
            mode: 0,
          },
        ],
        t3Options: [
          {
            name: 'Lunar spellbook',
            mode: 0,
          },
          {
            name: 'Melee Nylocas room',
            mode: 0,
          },
          {
            name: 'Whipped Trident Cheesecape 🍰',
            description: 'Bring a whip, a trident, and a cheesecape',
            mode: 0,
          },
          {
            name: 'No Barrages',
            mode: 0,
          },
          {
            name: 'Uncharged Scythe',
            mode: 0,
          },
          {
            name: 'Define Set up',
            mode: 0,
          },
          {
            name: 'Full void',
            mode: 0,
          },
          {
            name: 'No Stamina potion',
            mode: 0,
          },
          {
            name: 'Hard food & prayer pots only',
            mode: 0,
          },
          {
            name: 'No Piety, Rigour or Augury',
            mode: 0,
          },
          {
            name: '3 Brews entire raid',
            description: 'No other food allowed',
            mode: 0,
          },
          {
            name: 'No specs',
            mode: 0,
          },
          {
            name: 'No F keys',
            mode: 0,
          },
          {
            name: 'Free pass T3',
            mode: 0,
          },
        ]
      };
    },
    methods: {
      resetForfeits() {
        this.pickedForfeits = [];
      },
      getPrize() {
        setTimeout(() => {
          const options = this.t3enabled ? [...this.forfeitOptions, ...this.t3Options] : this.forfeitOptions;
          this.prizeIndex = Math.floor(Math.random() * options.length);
          this.$nextTick(this.$refs.vueLuckywheel.draw);
        }, 300);
      },
      gameOver() {
        const options = this.t3enabled ? [...this.forfeitOptions, ...this.t3Options] : this.forfeitOptions;
        this.pickedForfeits.push(options[this.prizeIndex]);
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
      },
    }
  };
</script>

<style lang="scss">


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
