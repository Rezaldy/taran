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
                                        min="0"
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
                            <q-item
                                    :class="{'bg-red-10': looter.active === false, 'bg-green-9': looter.active === true}"
                                    clickable
                                    v-ripple
                                    v-for="(looter, index) in lootListOptions"
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
                        <div></div>
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
    import {openURL} from 'quasar';
    import {fasDice, fasUndoAlt, fasList, fasSearch, fasPlus} from '@quasar/extras/fontawesome-v5';
    import AnimatedNumber from "animated-number-vue";
    import VueLuckywheel from 'vue-luckywheel';
    import VueLuckywheelItem from 'vue-luckywheel';
    import 'vue-luckywheel/lib/vue-luckywheel.css';
    import axios from 'axios';
    import {TimelineLite} from "gsap";
    import {SlideYUpTransition} from 'vue2-transitions';

    Vue.use(VueLuckywheel, VueLuckywheelItem);

    export default {
        components: {
            AnimatedNumber,
            SlideYUpTransition,
        },
        name: 'Home',
        data: function () {
            return {
                rng: {
                    min: 0,
                    max: 100,
                    value: 0
                },
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
                        name: 'No Magic',
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
                        name: 'No protection prayers',
                        mode: 0,
                    },
                    {
                        name: 'No Piety/Rigour/Augury',
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
                lootListOptions: [],
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
                    if (new Date().getMinutes() % 5) {
                        this.rng.value = 34;
                        this.$q.notify({
                            message: 'Gimme gimme.',
                            caption: 'Idiot.',
                            type: 'warning'
                        });
                    } else {
                        this.rng.value = Math.random() * (this.rng.max - this.rng.min) + this.rng.min;
                    }
                }
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
                        // \d\. a,(.*)\((.*)\) Regex for one line
                        if (this.lootInput.match(/(?:\d\. a,(.*)\((.*)\)\|[\n]?)/gm)) {
                            for (const lootCall of this.lootInput.match(/(?:\d\. a,(.*)\((.*)\)\|[\n]?)/gm)) {
                                const lootCallMatch = lootCall.match(/\d\. a,(.*)\((.*)\)\|/);
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
