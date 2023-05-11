<template>
  <v-app>
    <v-main>
      <v-bottom-navigation
          :value="value"
          color="teal"
          grow
      >
        <v-btn @click="visit = false">
          <span>Задания</span>
          <v-icon>mdi-briefcase-check</v-icon>
        </v-btn>

        <v-btn>
          <span @click="visit = true">Посещение точки</span>
          <v-icon>mdi-map-marker</v-icon>
        </v-btn>
      </v-bottom-navigation>
      <v-container fluid>
        <template>
          <v-container v-if="visit">
            <v-row class="ma-1 pa-1">
              <v-col cols="12" class="ma-2 pa-2">
                <v-autocomplete
                    v-model="objID"
                    :items="objs"
                    item-text="TITLE"
                    item-value="ID"
                    label="Выберите точку"
                    @keyup="getObjs"
                    outlined
                    dense
                >
                </v-autocomplete>
              </v-col>
              <v-col>
                <Blank :objs="objs" :objID="objID" :setObj="setObj"/>
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
import Blank from '@/components/Blank.vue'
import Process from "@/components/Process";
import axios from 'axios'

export default {
  name: 'App',
  components: {
    Blank,
    Process
  },
  data: () => ({
    value: 1,
    visit: false,
    tasks: false,
    items: [],
    search: '',
    objID: '',
    timerID: '',
    objs: [],
    url: 'https://dcoffee.bitrix24.ru/rest/10991/ljc1vj4mzg7sv7tp/',
  }),
  methods: {
    getItems(items) {
      this.items = items
    },
    setObj(items) {
      this.objs = items
    },
    async getObjs(e) {
      try {
          let isTrue = true;
          let start = 0;
          while (isTrue) {
            const params = {
              filter: {
                '%UF_CRM_1613562444155': e.target.value
              },
              select: ['ID', 'TITLE', 'UF_CRM_1613562444155'],
              start: start
            }
            const listObj = await axios.post(this.url + 'crm.company.list', params);
            for (const obj of listObj.data.result) {
              this.objs.push(obj)
            }
            if (listObj.data.next) start = listObj.data.next;
            else isTrue = false;
          }
          console.log(this.objs.length)
      } catch (e) {
        console.log(e);
      }
    }
  },
};
</script>
