<template>
  <div class="h-screen w-screen flex flex-col">
    <!-- enter the konami code if you're cool! -->
    <div v-if="konami" class="absolute h-full w-full flex justify-center items-center">
      <iframe
        class="w-screen h-screen"
        src="https://www.embed.com/app/arcade/asteroids.html"
        scrolling="no"
        frameBorder="0"
      ></iframe>
    </div>
    <div v-if="!loading && !konami" class="h-full sm:pl-32 text-white flex items-center sm:justify-left justify-center">
      <div class="w-full title">
        <div class="sm:text-left text-center leading-snug">
          <div class="sm:text-5xl text-4xl font-bold">👋 hey, i'm james.</div>
          <div class="manrope sm:text-2xl text-xl font-base text-gray-300 tracking-wide subpixel-antialiased">
            16-year-old software developer.
            <br />
            founder of
            <span class="italic hover:text-indigo-300"><a href="https://livecops.io">livecops</a>.</span>
          </div>
        </div>
        <div
          class="manrope mt-4 sm:px-0 px-6 flex flex-wrap sm:justify-start justify-center text-indigo-200 overflow-hidden tracking-wide font-medium"
        >
          <a
            href="https://github.com/JamesLovern"
            target="_blank"
            class="flex items-center mr-5 hover:text-indigo-300 mt-2"
          >
            <font-awesome-icon class="mr-2" :icon="['fab', 'github']" />
            <div class="text-xs">JamesLovern</div>
          </a>
          <a
            href="https://twitter.com/_jameslovern"
            target="_blank"
            class="flex items-center mr-5 hover:text-indigo-300 mt-2"
          >
            <font-awesome-icon class="mr-2" :icon="['fab', 'twitter']" />
            <div class="text-xs">@_jameslovern</div>
          </a>
          <a
            href="https://discord.com/users/615617007949316096"
            target="_blank"
            class="flex items-center mr-5 hover:text-indigo-300 mt-2"
          >
            <font-awesome-icon class="mr-2" :icon="['fab', 'discord']" />
            <div class="text-xs">Jazzzi#6120</div>
          </a>
          <a href="mailto:hey@lovern.io" target="_blank" class="flex items-center hover:text-indigo-300 mt-2">
            <font-awesome-icon class="mr-2" :icon="['fa', 'envelope']" />
            <div class="text-xs">hey@lovern.io</div>
          </a>
        </div>
        <div v-show="true" class="mt-4 sm:px-0 px-6 flex flex-wrap sm:justify-start justify-center text-sm opacity-75">
          <font-awesome-icon class="mr-4" :icon="['fab', 'python']" />
          <font-awesome-icon class="mr-4" :icon="['fab', 'js-square']" />
          <font-awesome-icon class="mr-4" :icon="['fab', 'node-js']" />
          <font-awesome-icon :icon="['fab', 'react']" />
        </div>
      </div>
    </div>
    <transition name="fade">
      <div v-if="lastFM && !konami" class="relative w-full flex justify-center items-center">
        <div
          class="manrope text-white p-8 absolute font-bold bottom-0 lg:right-0 text-green-500 text-sm opacity-50 text-center"
        >
          <font-awesome-icon class="mr-1" :icon="['fab', 'spotify']" /> Listening to
          <span class="italic">{{ lastFM.recenttracks.track[0].name }} </span>
          on Spotify
        </div>
        <div
          class="manrope text-white p-8 absolute font-bold bottom-0 lg:left-0 text-white-500 text-sm opacity-50 text-center invisible sm:visible"
        >
          Site by
          <span class="italic hover:text-indigo-300"><a href="https://github.com/ven">Ven</a></span>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import axios from 'axios';
const konami = require('../assets/konami.mp3');

export default {
  data() {
    return {
      loading: true,
      lastFM: null,
      konami: false,
      keys: [38, 38, 40, 40, 37, 39, 37, 39, 66, 65],
      isChecked: {
        0: false,
        1: false,
        2: false,
        3: false,
        4: false,
        5: false,
        6: false,
        7: false,
        8: false,
        9: false,
      },
      current: 0,
    };
  },
  created() {
    window.addEventListener('keyup', this.listen);
  },
  mounted() {
    axios
      .get(
        'https://ws.audioscrobbler.com/2.0/?method=user.getrecenttracks&user=JamesLovern&api_key=b1875bc0c40c941df88c4e61297bcf31&format=json&limit=1',
      )
      .then(response => {
        this.lastFM = response.data;
        this.loading = false;
      })
      .catch(e => (this.loading = false));

    setInterval(this.updateSpotify, 5000);
  },
  methods: {
    updateSpotify() {
      axios
        .get(
          'https://ws.audioscrobbler.com/2.0/?method=user.getrecenttracks&user=JamesLovern&api_key=b1875bc0c40c941df88c4e61297bcf31&format=json&limit=1',
        )
        .then(response => {
          this.lastFM = response.data;
        });
    },
    reset() {
      Object.keys(this.isChecked).forEach(k => {
        this.isChecked[k] = false;
      });
      this.current = 0;
    },
    listen(e) {
      const key = e.which || e.keyCode || e.detail;
      if (this.keys.includes(key)) {
        if (key === this.keys[this.current]) {
          this.current += 1;
          this.isChecked[this.current - 1] = true;
          if (this.current === this.keys.length) {
            this.trigger();
            this.reset();
          }
        } else {
          this.reset();
        }
      }
    },
    trigger() {
      var audio = new Audio(konami);
      audio.play();
      document.body.style.backgroundColor = 'black';
      this.konami = true;
    },
  },
};
</script>

<style lang="scss" scoped>
@keyframes slideInFromLeft {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(0);
  }
}

.title {
  animation: 1.5s cubic-bezier(0.22, 1, 0.36, 1) 0s 1 slideInFromLeft;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 1.5s cubic-bezier(0.22, 1, 0.36, 1);
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
