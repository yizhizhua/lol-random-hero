<script setup>
import { ref, reactive } from "vue";
import { invoke } from "@tauri-apps/api/tauri";

import axios from "axios";
import "./main_page.css";

const greetMsg = ref("");
const name = ref("");
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
  while (index_map.value.size < 50) {
    index = Math.floor(Math.random() * current_heros_list.value.length);
    console.log("get index ", index);
    index_map.value.add(index);
  }
};

const lol_data = await axios.get(
  "https://game.gtimg.cn/images/lol/act/img/js/heroList/hero_list.js"
);
if (lol_data) {
  current_heros_list.value = lol_data.data.hero;
  current_version.value = lol_data.data.version;

  get_random_index();
  hero_list.value = Array.from(index_map.value).map(
    (index) => current_heros_list.value[index]
  );
} else {
}

async function greet() {
  // Learn more about Tauri commands at https://tauri.app/v1/guides/features/command
  greetMsg.value = await invoke("greet", { name: name.value });
}
</script>

<template>
  <div>
    <div class="line" v-for="item in [0, 1, 2, 3, 4]">
      <div class="player">
        <img
          class="hero-card"
          v-for="index in [1, 2, 3, 4, 5]"
          :src="`https://game.gtimg.cn/images/lol/act/img/champion/${
            hero_list[item * 5 * 2 + index - 1].alias
          }.png`"
          alt=""
        />
      </div>
      <div class="player">
        <img
        class="hero-card"
          v-for="index in [6, 7, 8, 9, 10]"
          :src="`https://game.gtimg.cn/images/lol/act/img/champion/${
            hero_list[item * 5 * 2 + index - 1].alias
          }.png`"
          alt=""
        />
      </div>
    </div>
    <p>{{ current_version }}</p>
  </div>
</template>
