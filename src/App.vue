<template>
  <v-app>
    <v-main>
      <v-bottom-navigation :value="value" color="teal" grow>
        <v-btn @click="visit = false">
          <span>Задания</span>
          <v-icon>mdi-briefcase-check</v-icon>
        </v-btn>

        <v-btn @click="getObjs">
          <span @click="visit = true">Посещение точки</span>
          <v-icon>mdi-map-marker</v-icon>
        </v-btn>
      </v-bottom-navigation>
      <v-container fluid>
        <template>
          <v-container v-if="visit">
            <v-row v-if="loading" class="ma-1 pa-1">
              <v-col align="center">
                <v-progress-circular
                  :rotate="360"
                  :size="300"
                  :width="35"
                  color="teal"
                  :value="loadingValue"
                  v-if="loading"
                >
                  {{ loadingValue }}
                </v-progress-circular>
              </v-col>
            </v-row>
            <v-row v-else class="ma-1 pa-1">
              <v-col cols="12" class="ma-2 pa-2">
                <v-autocomplete
                  v-model="objID"
                  :items="objs"
                  item-text="TITLE"
                  item-value="ID"
                  label="Выберите точку"
                  @keyup="selectObj"
                  outlined
                  dense
                >
                </v-autocomplete>
              </v-col>
              <v-col>
                <Blank :objs="objs" :objID="objID" :setObj="setObj" />
              </v-col>
            </v-row>
          </v-container>
          <v-container v-else fluid>
            <Process />
          </v-container>
        </template>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import Blank from "@/components/Blank.vue";
import Process from "@/components/Process";
import axios from "axios";

export default {
  name: "App",
  components: {
    Blank,
    Process,
  },
  data: () => ({
    loading: false,
    interval: {},
    loadingValue: 0,
    value: 1,
    visit: false,
    tasks: false,
    items: [],
    search: "",
    objID: "",
    timerID: "",
    objs: [],
    url: "https://dcoffee.bitrix24.ru/rest/10991/ljc1vj4mzg7sv7tp/",
  }),
  methods: {
    pause(ms) {
      return new Promise((resolve) => {
        setTimeout(resolve, ms);
      });
    },
    getItems(items) {
      this.items = items;
    },
    setObj(items) {
      this.objs = items;
    },
    selectObj(e) {
      const selectTitle = e.target.value;
      const selectObjs = this.objs.filter((e) => e.TITLE == selectTitle);
      this.items = selectObjs;
    },
    async getObjs() {
      try {
        this.loading = true;
        const batchParams = {
          halt: 0,
          cmd: {},
        };
        const listObjsAll = await axios.post(this.url + "crm.company.list", {});
        const length = listObjsAll.data.total;
        let index = 0;
        for (let start = 0; start <= length; start += 50) {
          batchParams.cmd[
            index
          ] = `crm.company.list?select[0]=ID&select[1]=TITLE&select[2]=UF_CRM_1613562444155&start=${start}`;
          index += 1;
          if (index % 50 == 0 || start == length) {
            const batchResult = await axios.post(
              this.url + "batch",
              batchParams
            );
            for (const elemBatchindex in batchResult.data.result.result) {
              for (const obj of batchResult.data.result.result[
                elemBatchindex
              ]) {
                this.objs.push(obj);
              }
            }
            batchParams.cmd = {};
          }
          await this.pause(50);
        }
        this.loading = false;
      } catch (e) {
        console.log(e);
      }
    },
  },
  watch: {
    objID: async function (newObj) {
      const params = {
        IBLOCK_TYPE_ID: "lists",
        IBLOCK_ID: 151,
        FILTER: {
          PROPERTY_605: newObj,
        },
      };
      const obj = await axios.post(this.url + "lists.element.get", params);
      if (obj.data.result.length > 0) {
        alert("Внимание! на данной торговой точке уже стоит КМ!");
      }
    },
  },
};
</script>
<style scoped>
.v-progress-circular {
  margin: 1rem;
}
</style>
