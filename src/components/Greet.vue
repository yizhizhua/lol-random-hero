<script setup>
import { ref, reactive, computed } from "vue";
import { invoke } from "@tauri-apps/api/tauri";
import axios from "axios";
import "./main_page.css";

const playerHeros = ref(3);
const p1 = computed(() => Array.from({ length: playerHeros.value }, (x, i) => i));
const p2 = computed(() =>
  Array.from(
    { length: playerHeros.value * 2 - playerHeros.value },
    (x, i) => i + playerHeros.value
  )
);
/**
 * http data
 */
const current_version = ref("");
const current_heros_list = ref([]);
const index_map = ref(new Set());
const hero_list = ref([]);

/**
 * web apply data
 */
const get_random_index = () => {
  let index;
  index_map.value = new Set();
  while (index_map.value.size < playerHeros.value * 10) {
    index = Math.floor(Math.random() * current_heros_list.value.length);
    index_map.value.add(index);
  }
};

const changeNumber = (val) => {
  if ((val > 0 && val < 7) && val !== playerHeros.value) {
    playerHeros.value = val
    getHeros()
  }
}

const getHeros = () => {
  get_random_index();
  console.log("11");
  hero_list.value = Array.from(index_map.value).map(
    (index) => current_heros_list.value[index]
  );
};

const lol_data = await axios.get(
  "https://game.gtimg.cn/images/lol/act/img/js/heroList/hero_list.js"
);
if (lol_data) {
  current_heros_list.value = lol_data.data.hero;
  current_version.value = lol_data.data.version;

  getHeros();
} else {
}
</script>

<template>
  <div>
    <div class="opt-line">
     

      <button class="button2" @click="changeNumber(playerHeros - 1)">
        <span> - </span>
      </button>
      <button class="pushable" @click="getHeros">
        <span class="shadow"></span>
        <span class="edge"></span>
        <span class="front"> 再来一次 </span>
      </button>
      <button class="button2" @click="changeNumber(playerHeros + 1)">
        <span> + </span>
      </button>
    </div>
    <div class="line" v-for="item in [0, 1, 2, 3, 4]">
      <div class="player">
        <img
          class="hero-card"
          v-for="index in p1"
          :src="`https://game.gtimg.cn/images/lol/act/img/champion/${
            hero_list[item * playerHeros * 2 + index].alias
          }.png`"
          alt=""
        />
      </div>
      <div class="player">
        <img
          class="hero-card"
          v-for="index in p2"
          :src="`https://game.gtimg.cn/images/lol/act/img/champion/${
            hero_list[item * playerHeros * 2 + index].alias
          }.png`"
          alt=""
        />
      </div>
    </div>
    <p>{{ current_version }}</p>
  </div>
</template>

<style>
.opt-line {
  margin: 20px 25vw;
  display: flex;
  width: 50vw;
  justify-content: space-between;
}
.pushable {
  position: relative;
  background: transparent;
  padding: 0px;
  border: none;
  cursor: pointer;
  outline-offset: 4px;
  outline-color: deeppink;
  transition: filter 250ms;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
}

.shadow {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  background: hsl(226, 25%, 69%);
  border-radius: 8px;
  filter: blur(2px);
  will-change: transform;
  transform: translateY(2px);
  transition: transform 600ms cubic-bezier(0.3, 0.7, 0.4, 1);
}

.edge {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  border-radius: 8px;
  background: linear-gradient(
    to right,
    hsl(248, 39%, 39%) 0%,
    hsl(248, 39%, 49%) 8%,
    hsl(248, 39%, 39%) 92%,
    hsl(248, 39%, 29%) 100%
  );
}

.front {
  display: block;
  position: relative;
  border-radius: 8px;
  background: hsl(248, 53%, 58%);
  padding: 16px 32px;
  color: white;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu,
    Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 1.5px;
  font-size: 1rem;
  transform: translateY(-4px);
  transition: transform 600ms cubic-bezier(0.3, 0.7, 0.4, 1);
}

.pushable:hover {
  filter: brightness(110%);
}

.pushable:hover .front {
  transform: translateY(-6px);
  transition: transform 250ms cubic-bezier(0.3, 0.7, 0.4, 1.5);
}

.pushable:active .front {
  transform: translateY(-2px);
  transition: transform 34ms;
}

.pushable:hover .shadow {
  transform: translateY(4px);
  transition: transform 250ms cubic-bezier(0.3, 0.7, 0.4, 1.5);
}

.pushable:active .shadow {
  transform: translateY(1px);
  transition: transform 34ms;
}

.pushable:focus:not(:focus-visible) {
  outline: none;
}

.button2 {
  padding: 0.1em 0.25em;
  width: 6.85em;
  height: 4.2em;
  background-color: #212121;
  border: 0.08em solid #fff;
  border-radius: 0.3em;
  font-size: 12px;
}

.button2 span {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
  bottom: 0.4em;
  width: 4em;
  height: 2.5em;
  background-color: #212121;
  border-radius: 0.2em;
  font-size: 1.5em;
  color: #fff;
  border: 0.08em solid #fff;
  box-shadow: 0 0.4em 0.1em 0.019em #fff;
}

.button2 span:hover {
  transition: all 0.5s;
  transform: translate(0, 0.4em);
  box-shadow: 0 0 0 0 #fff;
}

.button2 span:not(hover) {
  transition: all 1s;
}
</style>
