<template>
  <v-row class="ma-1 pa-1">
    <v-dialog
      v-model="dialog"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          color="light-green lighten-1"
          class="button rounded-md white--text"
          dark
          v-bind="attrs"
          v-on="on"
          small
        >
          Добавить
        </v-btn>
      </template>
      <v-card>
        <v-toolbar
          dark
          color="light-blue accent-2"
          class="text--center subtitle-2 white--text"
        >
          <v-btn icon dark @click="dialog = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
          <v-toolbar-title>Форма добавления</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-row class="ma-2 pa-2">
            <v-autocomplete
              v-model="selectAction"
              :items="actions"
              label="Тип действия"
              outlined
              dense
            >
            </v-autocomplete>
          </v-row>
          <template v-if="saveItems.length > 0">
            <template v-for="(item, index) in saveItems">
              <template>
                <v-autocomplete
                  :key="index + '/' + 'machine'"
                  class="ma-2 pa-2"
                  v-model="item.machineID"
                  label="Наименование кофемашины"
                  :items="machines"
                  item-text="NAME"
                  item-value="ID"
                  :filter="filterMachine"
                  outlined
                  dense
                ></v-autocomplete>
                <v-file-input
                  :key="index + '/' + 'photo1'"
                  v-model="item.photo1"
                  placeholder="Фото км в интерьере"
                  prepend-icon=""
                  prepend-inner-icon="mdi-camera"
                  outlined
                  dense
                ></v-file-input>
                <v-file-input
                  :key="index + '/' + 'photo2'"
                  v-model="item.photo2"
                  placeholder="Фото км близко"
                  prepend-icon=""
                  prepend-inner-icon="mdi-camera"
                  outlined
                  dense
                ></v-file-input>
                <v-file-input
                  :key="index + '/' + 'photo3'"
                  v-model="item.photo3"
                  placeholder="Фото серийника"
                  prepend-icon=""
                  prepend-inner-icon="mdi-camera"
                  outlined
                  dense
                ></v-file-input>
                <v-file-input
                  :key="index + '/' + 'photo4'"
                  v-model="item.photo4"
                  placeholder="Фото доп. оборудования"
                  prepend-icon=""
                  prepend-inner-icon="mdi-camera"
                  outlined
                  dense
                ></v-file-input>
                <template v-if="selectAction === 'Cнятие'">
                  <v-autocomplete
                    :key="index + '/' + 'objs'"
                    v-model="item.newObjID"
                    :items="objs"
                    item-text="TITLE"
                    item-value="ID"
                    label="Куда снимаем"
                    @keyup="selectObj"
                    outlined
                    dense
                  >
                  </v-autocomplete>
                  <template v-if="item.btns.length > 0">
                    <template v-for="(btn, index) in item.btns">
                      <div
                        style="flex-direction: row; display: flex"
                        :key="
                          index +
                          '/' +
                          item.machineID +
                          '/' +
                          index +
                          '/' +
                          'div'
                        "
                      >
                        <v-text-field
                          :key="
                            index +
                            '/' +
                            item.machineID +
                            '/' +
                            index +
                            '/' +
                            'btnNumber'
                          "
                          class="ma-2 pa-2"
                          v-model="btn.number"
                          label="Кнопка"
                          type="number"
                          outlined
                          dense
                          :disabled="item.disabled"
                        ></v-text-field>
                        <v-autocomplete
                          :key="
                            index +
                            '/' +
                            item.machineID +
                            '/' +
                            index +
                            '/' +
                            'btnRecipe'
                          "
                          class="ma-2 pa-2"
                          v-model="btn.recipeID"
                          label="Рецептура"
                          :items="recipes"
                          item-text="NAME"
                          item-value="ID"
                          outlined
                          dense
                          :disabled="item.disabled"
                        ></v-autocomplete>
                        <v-text-field
                          :key="
                            index +
                            '/' +
                            item.machineID +
                            '/' +
                            index +
                            '/' +
                            'quntity'
                          "
                          class="ma-2 pa-2"
                          v-model="btn.cups"
                          label="Кол-во чашек"
                          outlined
                          dense
                        ></v-text-field>
                      </div>
                    </template>
                  </template>
                  <v-btn
                    class="ma-2 pa-2"
                    @click="addBtn(item)"
                    :key="index + '/' + item.machineID + '/' + 'btn'"
                    small
                  >
                    Добавить кнопку с рецептом
                  </v-btn>
                </template>
                <template
                  v-if="
                    selectAction === 'Установка' || selectAction === 'Проверка'
                  "
                >
                  <v-autocomplete
                    v-if="selectAction === 'Установка'"
                    :key="index + '/' + 'objs'"
                    v-model="item.newObjID"
                    :items="objs"
                    item-text="TITLE"
                    item-value="ID"
                    label="Откуда ставим"
                    :disabled="item.disabled"
                    @keyup="selectObj"
                    outlined
                    dense
                  >
                  </v-autocomplete>
                  <template v-if="item.btns.length > 0">
                    <template v-for="(btn, index) in item.btns">
                      <div
                        style="
                          flex-direction: row;
                          display: flex;
                          align-items: baseline;
                        "
                        :key="
                          index +
                          '/' +
                          item.machineID +
                          '/' +
                          index +
                          '/' +
                          'div'
                        "
                      >
                        <v-col cols="2">
                          <v-text-field
                            :key="
                              index +
                              '/' +
                              item.machineID +
                              '/' +
                              index +
                              '/' +
                              'btnNumber'
                            "
                            class="mr-1"
                            v-model="btn.number"
                            label="Номер кнопки"
                            type="number"
                            :disabled="item.disabled"
                            outlined
                            dense
                          ></v-text-field>
                        </v-col>
                        <v-col cols="5">
                          <v-autocomplete
                            :key="
                              index +
                              '/' +
                              item.machineID +
                              '/' +
                              index +
                              '/' +
                              'btnRecipe'
                            "
                            class="mr-1"
                            v-model="btn.recipeID"
                            label="Рецептура"
                            :items="recipes"
                            item-text="NAME"
                            item-value="ID"
                            :disabled="item.disabled"
                            outlined
                            dense
                          ></v-autocomplete>
                        </v-col>
                        <v-col cols="3">
                          <v-text-field
                            :key="
                              index +
                              '/' +
                              item.machineID +
                              '/' +
                              index +
                              '/' +
                              'quntity'
                            "
                            class="mr-1"
                            v-model="btn.cups"
                            label="Кол-во чашек"
                            outlined
                            dense
                          ></v-text-field>
                        </v-col>
                        <v-col cols="1">
                          <v-btn
                            icon
                            @click="deleteBtn(item, btn)"
                            :disabled="item.disabled"
                          >
                            <v-icon>mdi-window-close</v-icon>
                          </v-btn>
                        </v-col>
                      </div>
                    </template>
                  </template>
                  <template v-if="item.equips.length > 0">
                    <template v-for="(equip, indexEquip) in item.equips">
                      <div
                        style="
                          flex-direction: row;
                          display: flex;
                          align-items: baseline;
                        "
                        :key="
                          index +
                          '/' +
                          item.machineID +
                          '/' +
                          indexEquip +
                          '/' +
                          'Equip/div'
                        "
                      >
                        <v-col class="ma-2 pa-2" cols="8">
                          <v-autocomplete
                            :key="
                              index +
                              '/' +
                              item.machineID +
                              '/' +
                              indexEquip +
                              '/' +
                              'equips'
                            "
                            v-model="equip.ID"
                            label="Доп. оборудование"
                            :items="equips"
                            item-text="NAME"
                            item-value="ID"
                            :disabled="item.disabled"
                            outlined
                            dense
                          ></v-autocomplete>
                        </v-col>
                        <v-col class="ma-2 pa-2">
                          <v-text-field
                            v-model="equip.count"
                            label="кол-во"
                            outlined
                            dense
                          ></v-text-field>
                        </v-col>
                        <v-btn
                          icon
                          @click="deleteEquip(item, equip)"
                          :disabled="item.disabled"
                        >
                          <v-icon>mdi-window-close</v-icon>
                        </v-btn>
                      </div>
                    </template>
                  </template>
                  <v-btn
                    class="ma-2 pa-2"
                    @click="addBtn(item, item.btns.length + 1)"
                    :key="index + '/' + item.machineID + '/' + 'btn'"
                    :disabled="item.disabled"
                    small
                  >
                    Добавить кнопку с рецептом
                  </v-btn>
                  <v-btn
                    class="ma-2 pa-2"
                    @click="addEquips(item)"
                    :key="index + '/' + item.machineID + '/' + 'equips'"
                    :disabled="item.disabled"
                    small
                  >
                    Добавить доп. оборудование
                  </v-btn>
                </template>
              </template>
              <v-divider class="mt-3" :key="index + '/' + 'dividers'"></v-divider>
            </template>
          </template>
          <v-col class="mt-5" cols="12">
            <v-btn v-if="selectAction === 'Установка'" small @click="addRow"
              >Добавить кофемашину
            </v-btn>
          </v-col>
        </v-card-text>
        <v-toolbar
          dark
          color="light-blue accent-2"
          class="text--center subtitle-2 white--text"
        >
          <v-toolbar-items>
            <v-btn :disabled="disable" dark text @click="saveRow">
              Сохранить
            </v-btn>
          </v-toolbar-items>
        </v-toolbar>
      </v-card>
    </v-dialog>
  </v-row>
</template>

<script>
import axios from "axios";
//import {BX24} from 'bx24';

export default {
  name: "Blank-block",
  props: {
    objs: Array,
    objID: String,
  },
  data() {
    return {
      disable: false,
      dialog: false,
      photo1: "",
      photo2: "",
      photo3: "",
      photo4: "",
      selectAction: "",
      actions: ["Установка", "Cнятие", "Проверка"],
      selectObjs: [],
      saveItems: [],
      machines: [],
      recipes: [],
      equips: [],
      brands: {
        97: "Rheavendors",
        99: "Necta",
        101: "Kaffit",
        103: "Philips",
        189: "NUOVA SIMONELLI",
      },
      models: {
        187: "K95L",
        107: "XS",
        109: "XX-OC",
        111: "Calibri",
        113: "Koro",
        115: "Krea",
        117: "Nizza",
        119: "PRO",
        121: "Philips",
        143: "Saeco",
        145: "Термокружка",
        147: "Холодильник",
        149: "Вывеска",
        191: "APPIA 1GR",
        193: "APPIA 2GR",
        195: "Cool Mix",
        197: "BL Grande Pro",
        199: "BL eC",
        211: "К96L",
        213: "Терминал",
      },
      url: "https://dcoffee.bitrix24.ru/rest/10991/ljc1vj4mzg7sv7tp/",
      auth: "",
      domen: "",
    };
  },
  methods: {
    async getMachines() {
      try {
        const params = {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: 29,
          start: 0,
          auth: this.auth.ACCESS_TOKEN,
        };
        let isTrue = true;
        while (isTrue) {
          const list = await axios.post(this.url + "lists.element.get", params);
          console.log({ list });
          for (const elem of list.data.result) {
            const machineSerial = elem.PROPERTY_133
              ? Object.values(elem.PROPERTY_133)[0]
              : "";
            const machineModel = elem.PROPERTY_107
              ? this.models[Object.values(elem.PROPERTY_107)[0]]
              : "";
            const machineBrand = elem.PROPERTY_103
              ? this.brands[Object.values(elem.PROPERTY_103)[0]]
              : "";

            elem.NAME = `${machineSerial}/${machineModel}/${machineBrand}`;

            if (elem.PROPERTY_639) {
              if (Object.values(elem.PROPERTY_639) != "215") {
                this.machines.push(elem);
              }
            } else {
              this.machines.push(elem);
            }
          }
          if (list.data.next) params.start = list.data.next;
          else isTrue = false;
        }
        console.log(this.machines);
      } catch (e) {
        console.log(e);
      }
    },
    async getMachine(item) {
      try {
        const machineParams = {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: "29",
          ELEMENT_ID: item.machineID,
          auth: this.auth.ACCESS_TOKEN,
        };
        const selectMachine = await axios.post(
          this.url + "lists.element.get",
          machineParams
        );
        console.log({ selectMachine });
        item.machineSerial = selectMachine.data.result[0].PROPERTY_133
          ? Object.values(selectMachine.data.result[0].PROPERTY_133)[0]
          : "";
        item.machineModel = selectMachine.data.result[0].PROPERTY_107
          ? this.models[
              Object.values(selectMachine.data.result[0].PROPERTY_107)[0]
            ]
          : "";
        item.machineBrand = selectMachine.data.result[0].PROPERTY_103
          ? this.brands[
              Object.values(selectMachine.data.result[0].PROPERTY_103)[0]
            ]
          : "";
        if (item.action === "Установка") {
          const paramsBtn = {
            IBLOCK_TYPE_ID: "lists",
            IBLOCK_ID: 153,
            FILTER: {
              PROPERTY_615: item.machineID,
            },
            start: 0,
            auth: this.auth.ACCESS_TOKEN,
          };
          let isTrue = true;
          const btns = [];
          while (isTrue) {
            const listBtn = await axios.post(
              this.url + "lists.element.get",
              paramsBtn
            );
            btns.push(...listBtn.data.result);
            if (listBtn.data.next) paramsBtn.start = listBtn.data.next;
            else isTrue = false;
          }
          if (btns.length > 0) {
            for (const btn of btns) {
              item.btns.push({
                id: btn.ID,
                number: Object.values(btn.PROPERTY_609)[0],
                recipeID: Object.values(btn.PROPERTY_611)[0],
                cups: Object.values(btn.PROPERTY_613)[0],
                oldCups: Object.values(btn.PROPERTY_623)[0],
              });
            }
          } else {
            item.btns = [];
          }
        }
      } catch (e) {
        console.log(e);
      }
    },
    filterMachine(item, queryText) {
      console.log({ item });
      console.log({ queryText });
      if (item.PROPERTY_133) {
        console.log(Object.values(item.PROPERTY_133), "test 2");
        return (
          item.NAME.toLowerCase().indexOf(queryText.toLowerCase()) > -1 ||
          String(Object.values(item.PROPERTY_133))
            .toLowerCase()
            .indexOf(queryText.toLowerCase()) > -1
        );
      } else {
        console.log(item.PROPERTY_133, "test");
        return item.NAME.toLowerCase().indexOf(queryText.toLowerCase()) > -1;
      }
    },
    async getRecipes() {
      try {
        const params = {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: 145,
          start: 0,
          auth: this.auth.ACCESS_TOKEN,
        };
        let isTrue = true;
        while (isTrue) {
          const list = await axios.post(this.url + "lists.element.get", params);
          this.recipes.push(...list.data.result);
          if (list.data.next) params.start = list.data.next;
          else isTrue = false;
        }
      } catch (e) {
        console.log(e);
      }
    },
    async getEquips() {
      try {
        const params = {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: 143,
          start: 0,
          auth: this.auth.ACCESS_TOKEN,
        };
        let isTrue = true;
        while (isTrue) {
          const list = await axios.post(this.url + "lists.element.get", params);
          this.equips.push(...list.data.result);
          if (list.data.next) params.start = list.data.next;
          else isTrue = false;
        }
      } catch (e) {
        console.log(e);
      }
    },
    addBtn(item, index) {
      try {
        item.btns.push({
          id: index,
          number: "",
          recipeID: "",
          cups: "",
          oldCups: 0,
        });
      } catch (e) {
        console.log(e);
      }
    },
    addEquips(item) {
      try {
        item.equips.push({
          ID: "",
          newID: "",
          newObjID: "",
        });
      } catch (e) {
        console.log(e);
      }
    },
    addRow() {
      try {
        this.saveItems.push({
          objID: this.objID,
          objName: this.objs.filter((e) => e.ID == this.objID)[0].TITLE,
          action: this.selectAction,
          machineID: "",
          equips: [],
          btns: [],
          newMachineID: "",
          newObjID: "",
          disabled: false,
        });
        console.log("add machine");
      } catch (e) {
        console.log(e);
      }
    },
    deleteBtn(item, btn) {
      const indexBtn = item.btns.findIndex((e) => e.id == btn.id);
      item.btns.splice(indexBtn, 1);
    },
    deleteEquip(item, equip) {
      const indexBtn = item.equips.findIndex((e) => e.ID == equip.ID);
      item.equips.splice(indexBtn, 1);
    },
    async saveInstallation(item, date, equipsIDs) {
      try {
        //если установка
        //создаем кнопки
        const batchParams = {
          halt: 0,
          cmd: {},
          auth: this.auth.ACCESS_TOKEN,
        };
        const btnIDs = [];
        console.log(item.btns, "btns");
        for (const btnIndex in item.btns) {
          console.log(btnIndex);
          batchParams.cmd[
            `btn_${btnIndex}`
          ] = `lists.element.add?IBLOCK_TYPE_ID=lists&IBLOCK_ID=153&ELEMENT_CODE=btn_${btnIndex}/${date}&FIELDS[PROPERTY_609]=${item.btns[btnIndex].number}&FIELDS[PROPERTY_611]=${item.btns[btnIndex].recipeID}&FIELDS[PROPERTY_613]=${item.btns[btnIndex].cups}&FIELDS[PROPERTY_623]=${item.btns[btnIndex].oldCups}&&FIELDS[PROPERTY_615]=${item.machineID}&FIELDS[NAME]=Кнопка ${item.btns[btnIndex].number}`;
          console.log({ batchParams });
          if (btnIndex >= 50 || btnIndex == Number(item.btns.length) - 1) {
            const batchResult = await axios.post(
              this.url + "batch",
              batchParams
            );
            batchParams.cmd = {};
            console.log({ batchResult });
            for (const elemBatchIndex in batchResult.data.result.result) {
              btnIDs.push(batchResult.data.result.result[elemBatchIndex]);
            }
          }
        }

        //создаем точку
        await axios.post(this.url + "lists.element.add", {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: "151",
          ELEMENT_CODE: item.objID,
          FIELDS: {
            NAME: item.objName,
            PROPERTY_605: item.objID,
            PROPERTY_607: item.machineID,
            PROPERTY_617: btnIDs,
            PROPERTY_619: equipsIDs,
          },
          auth: this.auth.ACCESS_TOKEN,
        });
      } catch (e) {
        console.log(e);
      }
    },
    async saveCheck(item) {
      try {
        console.log("Проверка");
        const date = Date.now();
        //если проверка
        //редактируем кнопки
        const batchParams = {
          halt: 0,
          cmd: {},
          auth: this.auth.ACCESS_TOKEN,
        };
        console.log(item.btns, "btns");
        for (const btnIndex in item.btns) {
          console.log(btnIndex);
          batchParams.cmd[
            `btn_${btnIndex}`
          ] = `lists.element.update?IBLOCK_TYPE_ID=lists&IBLOCK_ID=153&ELEMENT_ID=${item.btns[btnIndex].id}&FIELDS[PROPERTY_609]=${item.btns[btnIndex].number}&FIELDS[PROPERTY_611]=${item.btns[btnIndex].recipeID}&FIELDS[PROPERTY_613]=${item.btns[btnIndex].cups}&FIELDS[PROPERTY_623]=${item.btns[btnIndex].oldCups}&FIELDS[PROPERTY_615]=${item.machineID}&FIELDS[NAME]=Кнопка ${item.btns[btnIndex].number}`;
          batchParams.cmd[
            `log_${btnIndex}`
          ] = `lists.element.add?IBLOCK_TYPE_ID=lists&IBLOCK_ID=155&ELEMENT_CODE=log_${date}/log_${btnIndex}&FIELDS[NAME]=Лог&FIELDS[PROPERTY_625]=${item.machineID}&FIELDS[PROPERTY_627]=${item.btns[btnIndex].id}&FIELDS[PROPERTY_629]=${item.btns[btnIndex].cups}`;
          if (btnIndex > 49 || btnIndex == Number(item.btns.length) - 1) {
            console.log({ batchParams });
            const batchResult = await axios.post(
              this.url + "batch",
              batchParams
            );
            console.log({ batchResult });
            batchParams.cmd = {};
          }
          item.btns[btnIndex].oldCups = item.btns[btnIndex].cups;
        }
      } catch (e) {
        console.log(e);
      }
    },
    async saveRemove(item, date, equipsIDs) {
      try {
        //если снятие
        //убираем из точки км
        //TODO сделать снятие, при снятии указывается куда снимается
        console.log(item);
        console.log(date);
        console.log(equipsIDs);
        await this.saveCheck(item);
        await axios.post(this.url + "lists.element.delete", {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: "151",
          ELEMENT_ID: item.id,
          auth: this.auth.ACCESS_TOKEN,
        });
      } catch (e) {
        console.log(e);
      }
    },
    async addPhoto(type, file) {
      try {
        const getBase64 = (file) =>
          new Promise((resolve) => {
            const reader = new FileReader();
            reader.onloadend = () => {
              const base64String = reader.result
                .replace("data:", "")
                .replace(/^.+,/, "");
              resolve(base64String);
              //console.log(base64String);
            };
            reader.readAsDataURL(file);
          });

        const diskID = await axios.post(
          this.url + "disk.storage.getforapp",
          {}
        );
        console.log({ diskID });
        const fileID = await axios.post(this.url + "disk.storage.uploadfile", {
          id: 3, //diskID[0].ID,
          data: { NAME: file.name },
          fileContent: [file.name, await getBase64(file)],
          generateUniqueName: "Y",
          auth: this.auth.ACCESS_TOKEN,
        });

        switch (type) {
          case 1:
            this.photo1 = ["n" + fileID[0].ID];
            break;
          case 2:
            this.photo2 = ["n" + fileID[0].ID];
            break;
          case 3:
            this.photo3 = ["n" + fileID[0].ID];
            break;
          case 4:
            this.photo4 = ["n" + fileID[0].ID];
            break;
        }
      } catch (e) {
        console.log(e);
      }
    },
    async saveRow() {
      try {
        const date = Date.now();
        const equipsIDs = [];
        console.log(this.saveItems);
        for (const item of this.saveItems) {
          if (item.equips.length > 0) {
            //формируем массив доп. оборудования
            for (const equip of item.equips) {
              equipsIDs.push(equip["ID"]);
            }
          }

          switch (item.action) {
            case "Установка":
              await this.saveInstallation(item, date, equipsIDs);
              break;
            case "Cнятие":
              await this.saveRemove(item, date, equipsIDs);
              break;
            case "Проверка":
              await this.saveCheck(item);
              break;
          }

          await this.addPhoto(1, item.photo1);
          await this.addPhoto(2, item.photo2);
          await this.addPhoto(3, item.photo3);
          await this.addPhoto(4, item.photo4);
          //создаем лог
          await axios.post(this.url + "lists.element.add", {
            IBLOCK_TYPE_ID: "lists",
            IBLOCK_ID: "149",
            ELEMENT_CODE: `log_${date}`,
            FIELDS: {
              NAME: item.action,
              PROPERTY_621: item.objID,
              PROPERTY_599: item.machineID,
              PROPERTY_603: item.action,
              PROPERTY_601: equipsIDs,
              PROPERTY_631: item.newObjID,
              PROPERTY_633: { n0: this.photo1 },
              PROPERTY_635: { n0: this.photo2 },
              PROPERTY_637: { n0: this.photo3 },
              PROPERTY_657: { n0: this.photo4 },
            },
            auth: this.auth.ACCESS_TOKEN,
          });
        }
        this.dialog = false;
      } catch (e) {
        console.log(e);
      }
    },
    selectObj(e) {
      const selectTitle = e.target.value;
      const selectObjs = this.objs.filter((e) => e.TITLE == selectTitle);
      this.items = selectObjs;
    },
    async getObj() {
      const objParams = {
        IBLOCK_TYPE_ID: "lists",
        IBLOCK_ID: "151",
        ELEMENT_CODE: this.objID,
        auth: this.auth.ACCESS_TOKEN,
      };
      const objData = await axios.post(
        this.url + "lists.element.get",
        objParams
      );
      console.log({ objData });

      if (objData.data.result.length > 0) {
        const item = {
          id: objData.data.result[0].ID,
          objID: this.objID,
          objName: this.objs.filter((e) => e.ID == this.objID)[0].TITLE,
          action: this.selectAction,
          machineID: Object.values(objData.data.result[0].PROPERTY_607)[0],
          equips: [],
          btns: [],
          newObjID: "",
          machineSerial: "",
          machineModel: "",
          machineBrand: "",
          disabled: true,
        };
        await this.getMachine(item);
        const batchParams = {
          halt: 0,
          cmd: {},
          auth: this.auth.ACCESS_TOKEN,
        };
        if (objData.data.result[0].PROPERTY_617) {
          const btns = Object.values(objData.data.result[0].PROPERTY_617);
          for (const btnIndex in btns) {
            batchParams.cmd[
              `btn_${btnIndex}`
            ] = `lists.element.get?IBLOCK_TYPE_ID=lists&IBLOCK_ID=153&ELEMENT_ID=${btns[btnIndex]}`;
            if (btnIndex > 49 || btnIndex == Number(btns.length) - 1) {
              const batchResult = await axios.post(
                this.url + "batch",
                batchParams
              );
              batchParams.cmd = {};
              const batchRes = batchResult.data.result.result;
              for (const elemBatchIndex in batchRes) {
                item.btns.push({
                  id: batchRes[elemBatchIndex][0].ID,
                  number: Object.values(
                    batchRes[elemBatchIndex][0].PROPERTY_609
                  )[0],
                  recipeID: Object.values(
                    batchRes[elemBatchIndex][0].PROPERTY_611
                  )[0],
                  cups: Object.values(
                    batchRes[elemBatchIndex][0].PROPERTY_613
                  )[0],
                  oldCups: Object.values(
                    batchRes[elemBatchIndex][0].PROPERTY_623
                  )[0],
                });
              }
            }
          }
        }
        if (objData.data.result[0].PROPERTY_619) {
          const equips = Object.values(objData.data.result[0].PROPERTY_619);
          for (const equipIndex in equips) {
            batchParams.cmd[
              `equip_${equipIndex}`
            ] = `lists.element.get?IBLOCK_TYPE_ID=lists&IBLOCK_ID=143&ELEMENT_ID=${equips[equipIndex]}`;
            if (equipIndex > 49 || equipIndex == Number(equips.length) - 1) {
              const batchResult = await axios.post(
                this.url + "batch",
                batchParams
              );
              batchParams.cmd = {};
              const batchRes = batchResult.data.result.result;
              for (const elemBatchIndex in batchRes) {
                item.equips.push({
                  ID: batchRes[elemBatchIndex][0].ID,
                  newID: "",
                  newObjID: "",
                });
              }
            }
          }
        }

        this.saveItems.push(item);
        console.log(this.saveItems, "items");
      }
    },
  },
  watch: {
    selectAction(newAction) {
      if (newAction === "Проверка" || newAction === "Cнятие") {
        console.log({ newAction });
        this.saveItems = [];
        this.getObj();
      } else {
        this.saveItems = [];
      }
    },
  },
  async mounted() {
    //const bx24 = new BX24();
    // this.auth = await bx24.getAuth()
    // this.url = `https://${this.auth.DOMAIN}/rest/`
    // this.domen = this.auth.DOMAIN

    this.getMachines();
    this.getRecipes();
    this.getEquips();
  },
};
</script>

<style scoped>
.col {
  margin: 0;
  padding: 0;
  margin-right: 5px;
}

.v-application p:not(:last-child) {
  margin-bottom: 0;
}

.machine__description {
  margin-bottom: 16px;
}
</style>
