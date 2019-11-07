<template>
  <q-page class="main" ref="scrollArea">
    <q-scroll-observer @scroll="scrollHandler" debounce="0"/>
    <div class="main__video-container flex flex-center">
      <video :style="{opacity: secondOpacity}" autoplay class="main__video-container__second" loop muted
             ref="secondVideo">
        <source src="http://api.cloud.sberlabs.com:9080/video/m2.mp4" type="video/mp4">
      </video>
      <video
        :style="{ 'width': `${currentWidth}px`, opacity: firstOpacity, transition: `width .${currentTransition}s, opacity .5s` }"
        autoplay class="main__video-container__first" loop muted>
        <source src="http://api.cloud.sberlabs.com:9080/video/m1.mp4" type="video/mp4">
      </video>

      <transition name="fade-up">
        <div class="main__video-container__text main__video-container__text-1 flex column" key="first"
             v-if="currentText === 'first'">
          <span>Новый суперкомпьютер</span>
          <span>Колумбус</span>
        </div>

        <div class="main__video-container__text main__video-container__text-2 flex column" key="second"
             v-if="currentText === 'second'">
          <span>Эффективная мощность</span>
          <span>6.7 Петафлопс</span>
        </div>

        <div class="main__video-container__text main__video-container__text-3 flex column" key="third"
             v-if="currentText === 'third'">
          <span>Это</span>
          <span>#1 в России</span>
          <span>по производительности</span>
        </div>

        <div class="main__video-container__text main__video-container__text-4 flex column" key="fourth"
             v-if="currentText === 'fourth'">
          <span>Кристофари</span>
          <span>доступен <br>с 12.12.2019 </span>
        </div>
      </transition>

      <transition name="fade">
        <button class="main__video-container__button cursor-pointer" v-if="currentText === 'fourth'">Оставить заявку
        </button>
      </transition>

      <transition name="fade">
        <a @click.prevent="dialog = true" class="main__video-container__link flex justify-between items-center"
           href v-show="linkDisplay">
          <img alt="play" src="../assets/play.svg">
          <span>Смотреть видео</span>
        </a>
      </transition>

      <transition name="fade">
        <div @click="setScroll" class='icon-scroll cursor-pointer' v-show="linkDisplay"></div>
      </transition>
    </div>

    <q-dialog maximized persistent transition-hide="fade" transition-show="fade" v-model="dialog">
      <div class="main__full-video flex flex-center">
        <q-icon @click="dialog = false" class="cursor-pointer" color="white" name="close"/>
        <video autoplay loop muted>
          <source src="http://api.cloud.sberlabs.com:9080/video/m1.mp4" type="video/mp4">
        </video>
      </div>
    </q-dialog>
  </q-page>
</template>

<script>
    import {scroll} from 'quasar'

    const {setScrollPosition} = scroll;

    export default {
        name: 'PageIndex',
        data() {
            return {
                currentWidth: 0,
                firstOpacity: 1,
                secondOpacity: 0,
                linkDisplay: true,
                currentTransition: 1,
                timeReset: false,
                dialog: false,
                currentText: 'first'
            }
        },
        computed: {
            deviceWidth() {
                if (window.innerWidth >= 1200) {
                    return 1380
                } else if (window.innerWidth < 1200 && window.innerWidth > 768) return 1200;
                else return 800
            }
        },
        methods: {
            scrollHandler(details) {
                this.linkDisplay = !(details.position > 0);
                if (details.position < 7300) {
                    this.currentWidth = this.deviceWidth + (details.position / 4)
                } else {
                    this.currentWidth = 3600
                }

                if (details.position >= 7300) {
                    this.currentTransition = 3;
                    if (details.position >= 7600) this.currentTransition = 3;
                    this.firstOpacity = 0;
                    this.currentText = 'fourth';
                    this.timeReset = true;
                    if (this.timeReset && details.position < 7350) {
                        this.$refs.secondVideo.currentTime = 2
                    }
                } else {
                    if (details.position >= 5000) this.currentText = 'third';
                    else if (details.position >= 2500) this.currentText = 'second';
                    else this.currentText = 'first';
                    this.timeReset = false;
                    this.currentTransition = 3;
                    this.firstOpacity = 1
                }

                if (this.firstOpacity === 0 && details.position >= 7000) {
                    this.secondOpacity = 1
                } else {
                    this.secondOpacity = 0
                }
            },
            setScroll() {
                setScrollPosition(window, 500)
            }
        }
    }
</script>
