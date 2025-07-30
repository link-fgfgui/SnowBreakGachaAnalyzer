<template>
  <v-app-bar :title="i18n[lang].summary"></v-app-bar>
  <v-snackbar v-model="ErrorSnackbar.active"
    >{{ ErrorSnackbar.text }}
    <template v-slot:actions>
      <v-btn
        icon="mdi-close"
        @click="ErrorSnackbar.active = !ErrorSnackbar.active"
      >
      </v-btn>
    </template>
  </v-snackbar>
  <v-container class="pa-8" fluid>
    <v-row cols="12">
      <v-file-input
        :label="i18n[lang].file"
        v-model="fileObj"
        :disabled="!fileInputProp.active"
        :loading="fileInputProp.loading"
        accept=".json"
        :clearable="false"
        prepend-icon="mdi-code-json"
        variant="underlined"
      ></v-file-input>
    </v-row>
    <v-row cols="12">
      <v-col
        v-for="(poolName, poolIndex) in summaryResult.PoolList"
        v-show="chartData.PoolListEnable[poolIndex]"
        style="max-width: 400px"
      >
        <v-label style="font-weight: bold">{{ poolName }}</v-label>
        <v-label v-for="ttt in summaryResult[poolName].texts"
          >&nbsp;{{ ttt }}</v-label
        >
        <v-btn-toggle
          v-model="chartData.RaritiesStatus[poolName]"
          color="primary"
          class="full-width"
          multiple
        >
          <v-btn
            :style="
              'width: ' + ((100 / summaryResult.Rarities.length) | 0) + '%;'
            "
            v-for="star in summaryResult.Rarities"
            >{{ star }}{{ starEmoji }}</v-btn
          >
        </v-btn-toggle>
        <v-card class="mx-auto">
          <v-list style="max-height: 400px; max-width: 400px">
            <v-list-item
              v-for="pull in summaryResult[poolName].content"
              v-show="test(poolName, pull.star)"
              ><div class="d-flex justify-space-between">
                <span>{{ pull.name }}</span>
                <span>{{ pull.toLastRari }}</span>
              </div>
              <v-progress-linear
                :color="
                  getLinearColor(pull.toLastRari / chartData.PoolGMs[poolIndex])
                "
                height="10"
                :model-value="
                  (pull.toLastRari / chartData.PoolGMs[poolIndex]) * 100
                "
                striped
              ></v-progress-linear
            ></v-list-item>
          </v-list>
        </v-card>
      </v-col>
    </v-row>
    <v-row cols="12" v-show="armors.length !== 0">
      <v-list style="width: 100%; margin-left: 20px; margin-right: 20px">
        <v-list-item v-for="armor in armors"
          ><div class="d-flex justify-space-between">
            <span>{{ armor[0] }}</span>
            <span>{{ armor[1] }}</span>
          </div>
        </v-list-item>
      </v-list>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref, watch } from "vue";
function test(p1, p2) {
  if (chartData.value.RaritiesStatus[p1] === undefined) {
    chartData.value.RaritiesStatus[p1] = [];
  }
  return chartData.value.RaritiesStatus[p1].includes(
    summaryResult.value.Rarities.indexOf(p2)
  );
}
const starEmoji = ref("★");
function getLinearColor(i) {
  if (i > 0.8) {
    return "red-darken-4";
  } else {
    return "green-darken-3";
  }
}
const summaryResultExampleData = {
  Rarities: [4, 5],

  PoolList: [
    "SpecialCharacterHistory",
    "SpecialWeaponHistory",
    "SpecialCharacterHistoryMihoyo",
    "SpecialWeaponHistoryMihoyo",
    "CommonCharacterHistory",
    "CommonWeaponHistory",
  ],
  SpecialCharacterHistory: {
    texts: ["2023/06/30 17:34:32 - 2025/06/07 23:09:13", "总抽数: 480"],
    content: [{ name: "但战斗还未结束", star: 4, toLastRari: 2 }],
  },
  SpecialWeaponHistory: {
    texts: ["2023/06/30 17:34:32 - 2025/06/07 23:09:13", "总抽数: 480"],
    content: [{ name: "但战斗还未结束", star: 4, toLastRari: 2 }],
  },
  SpecialCharacterHistoryMihoyo: {
    texts: ["2023/06/30 17:34:32 - 2025/06/07 23:09:13", "总抽数: 480"],
    content: [{ name: "但战斗还未结束", star: 4, toLastRari: 2 }],
  },
  SpecialWeaponHistoryMihoyo: {
    texts: ["2023/06/30 17:34:32 - 2025/06/07 23:09:13", "总抽数: 480"],
    content: [{ name: "但战斗还未结束", star: 4, toLastRari: 2 }],
  },
  CommonCharacterHistory: {
    texts: ["2023/06/30 17:34:32 - 2025/06/07 23:09:13", "总抽数: 480"],
    content: [{ name: "但战斗还未结束", star: 4, toLastRari: 2 }],
  },
  CommonWeaponHistory: {
    texts: ["2023/06/30 17:34:32 - 2025/06/07 23:09:13", "总抽数: 480"],
    content: [{ name: "但战斗还未结束", star: 4, toLastRari: 2 }],
  },
};
const chartDataExampleData = {
  RaritiesStatus: {},
  PoolListEnable: [true, true, false, false, true, true],
  PoolGMs: [100, 100, 80, 80, 100, 100],
};
const summaryResult = ref({});
const chartData = ref();
const armors = ref([]);
// summaryResult.value = summaryResultExampleData; //dev
chartData.value = chartDataExampleData; //dev
// Error Show
const ErrorSnackbar = ref({ text: "", active: false });
function showError(error) {
  let msg;
  if (error instanceof Error) {
    msg = error.toString();
  } else {
    msg = String(error);
  }
  ErrorSnackbar.value.text = msg;
  ErrorSnackbar.value.active = true;
  console.error(error);
}

// i18n
const languages = navigator.languages;
let lang = ref("en");
const i18n = {
  en: {
    summary: "Summary",
    file: "Choose Gacha File",
    fullpull: "Full Pulls",
    pull: "Pulls",
    avg: "AVG",
  },
  "zh-CN": {
    summary: "尘白禁区抽卡文件分析",
    file: "选择导出的抽卡文件",
    fullpull: "总抽数",
    pull: "抽",
    avg: "平均",
  },
};
function tr(t) {
  return i18n[lang.value][t];
}
for (let i = 0; i < languages.length; i++) {
  const element = languages[i];
  if (i18n[element] !== undefined) {
    lang.value = element;
    break;
  }
}
// file input template props
const fileObj = ref(undefined);
const fileInputProp = ref({
  active: true,
  loading: false,
});
async function refreshPools(f) {
  console.log(f);
  let text = await f.text();
  const GachaJson = JSON.parse(text);
  text = undefined;
  let sRT = {};
  let armorsT = {};
  let sRTRarities = [];
  for (const [key, value] of Object.entries(GachaJson)) {
    for (let i = value.length - 1; i >= 0; i--) {
      const pull = value[i];

      if (pull.Star > 3) {
        let nameT = pull.Star + starEmoji.value + " " + pull.Name;
        if (armorsT[nameT] === undefined) {
          armorsT[nameT] = 1;
        } else {
          armorsT[nameT]++;
        }
      }

      if (!sRTRarities.includes(pull.Star)) {
        sRTRarities.push(pull.Star);
      }
    }
  }
  const armorsTT = Object.entries(armorsT);
  armorsTT.sort((a, b) => b[1] - a[1]);
  armors.value = armorsTT;

  sRT["Rarities"] = sRTRarities;
  sRT["PoolList"] = Object.keys(GachaJson);
  for (const [poolName, poolContent] of Object.entries(GachaJson)) {
    if (poolContent.length === 0) {
      sRT[poolName] = {
        texts: [],
        content: [],
      };
      continue;
    }
    let toLastRariObj = {};
    for (let i = 0; i < sRTRarities.length; i++) {
      toLastRariObj[sRTRarities[i]] = 0;
    }
    sRT[poolName] = {};
    sRT[poolName]["texts"] = [];
    sRT[poolName]["texts"].push(
      poolContent[poolContent.length - 1].Time + " > " + poolContent[0].Time
    );
    sRT[poolName]["content"] = [];
    let statistics = { allPulls: 0, RarityPulls: {} };
    for (let rryn = 0; rryn < sRTRarities.length; rryn++) {
      statistics.RarityPulls[sRTRarities[rryn]] = 0;
    }
    for (let i = poolContent.length - 1; i >= 0; i--) {
      const pull = poolContent[i];
      statistics.allPulls++;
      statistics.RarityPulls[pull.Star]++;
      //add 1 pull to every star level
      for (const rltokey in toLastRariObj) {
        if (toLastRariObj.hasOwnProperty(rltokey)) {
          toLastRariObj[rltokey]++;
        }
      }
      //get tlr
      let tlr = toLastRariObj[pull.Star];
      toLastRariObj[pull.Star] = 0;
      sRT[poolName]["content"].push({
        name: pull.Name,
        star: pull.Star,
        toLastRari: tlr,
      });
    }
    sRT[poolName]["texts"].push(tr("fullpull") + ": " + statistics.allPulls);
    for (let iii = 0; iii < sRTRarities.length; iii++) {
      const element = sRTRarities[iii];
      let toAg = [0, 0];
      for (let iiiii = 0; iiiii < sRT[poolName]["content"].length; iiiii++) {
        const element2 = sRT[poolName]["content"][iiiii];
        if (element2.star == element) {
          toAg[0]++;
          toAg[1] += element2.toLastRari;
        }
      }
      console.log(statistics.fullpull);

      sRT[poolName]["texts"].push(
        element +
          starEmoji.value +
          ": " +
          statistics.RarityPulls[element] +
          " [" +
          (
            (statistics.RarityPulls[element] / statistics.allPulls) *
            100
          ).toFixed(2) +
          "%] (" +
          tr("avg") +
          " " +
          (toAg[1] / toAg[0]).toFixed(2) +
          tr("pull") +
          " )"
      );
    }
  }
  summaryResult.value = sRT;
  // await new Promise((r) => setTimeout(r, 2000));
}
watch(fileObj, (newF, oldF) => {
  if (newF === undefined) {
    return;
  } else {
    fileInputProp.value.active = false;
    fileInputProp.value.loading = true;
    refreshPools(newF)
      .catch((e) => {
        showError(e);
      })
      .finally(() => {
        fileInputProp.value.active = true;
        fileInputProp.value.loading = false;
      });
  }
});
</script>
<style>
.full-width {
  width: 100%;
}
</style>
