<template>
  <v-container class="flex-fill">
    <v-row>
      <v-col>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Поиск"
          single-line
          dense
          outlined
          background-color="white"
          color="light-blue accent-2"
          hide-details
          @input="searchItems"
        ></v-text-field>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-list>
          <v-list-item-group
            color="light-blue accent-2"
            class="text--center subtitle-2 white--text"
          >
            <template v-for="(item, index) in filterItems">
              <v-list-item :key="item.id" @click="processOpen(item)">
                <v-list-item-content>
                  <v-list-item-title v-text="item.name"></v-list-item-title>
                  <template v-for="(row, index) in item.descriptionRows">
                    <v-list-item-subtitle
                      v-html="row.value"
                      :key="index"
                      v-if="index < 4"
                    ></v-list-item-subtitle>
                  </template>
                </v-list-item-content>
                <v-list-item-icon>
                  <v-icon> mdi-chevron-right</v-icon>
                </v-list-item-icon>
              </v-list-item>
              <v-divider :key="index"></v-divider>
              <v-dialog
                :key="index + '/' + item.id"
                v-model="item.proccessCard"
                fullscreen
                hide-overlay
                transition="dialog-bottom-transition"
                scrollable
              >
                <div class="white fill-height pb-10" style="overflow-y: scroll">
                  <v-card elevation="0">
                    <v-card-text>
                      <v-row>
                        <v-col cols="2">
                          <v-btn
                            icon
                            @click="item.proccessCard = false"
                            x-large
                          >
                            <v-icon>mdi-chevron-left</v-icon>
                          </v-btn>
                        </v-col>
                        <v-col>
                          <h3 class="light-blue--text mt-2">{{ item.name }}</h3>
                        </v-col>
                      </v-row>
                    </v-card-text>
                    <v-card-text>
                      <v-card-subtitle>
                        <template v-for="(row, index) in item.descriptionRows">
                          <template>
                            <div :key="item.name + '/' + index" class="pb-2">
                              {{ row.value }}
                            </div>
                          </template>
                        </template>
                      </v-card-subtitle>
                    </v-card-text>
                    <v-card-text
                      style="
                        display: flex;
                        justify-content: center;
                        align-items: center;
                      "
                    >
                      <v-form v-if="!isDone" style="width: 100%">
                        <template v-if="item.StatusOkLabel != 'Взять в работу'">
                          <v-autocomplete
                            :key="index + '/' + 'machine'"
                            v-model="item.obj.machineID"
                            label="Наименование кофемашины"
                            :items="machines"
                            item-text="NAME"
                            item-value="ID"
                            :messages="[
                              `${item.obj.machineBrand}/${item.obj.machineModel}/${item.obj.machineSerial}`,
                            ]"
                            :disabled="item.disabled"
                            outlined
                            dense
                          ></v-autocomplete>
                          <v-file-input
                            :key="index + '/' + 'photo1'"
                            v-model="item.obj.photo1"
                            placeholder="Фото км в интерьере"
                            prepend-icon=""
                            prepend-inner-icon="mdi-camera"
                            outlined
                            dense
                          ></v-file-input>
                          <v-file-input
                            :key="index + '/' + 'photo2'"
                            v-model="item.obj.photo2"
                            placeholder="Фото км близко"
                            prepend-icon=""
                            prepend-inner-icon="mdi-camera"
                            outlined
                            dense
                          ></v-file-input>
                          <v-file-input
                            :key="index + '/' + 'photo3'"
                            v-model="item.obj.photo3"
                            placeholder="Фото серийника"
                            prepend-icon=""
                            prepend-inner-icon="mdi-camera"
                            outlined
                            dense
                          ></v-file-input>
                          <v-autocomplete
                            :key="index + '/' + 'cause'"
                            v-model="item.cause"
                            label="Причины"
                            :items="causes"
                            item-text="NAME"
                            item-value="ID"
                            outlined
                            dense
                          ></v-autocomplete>
                          <v-autocomplete
                            :key="index + '/' + 'sparePart'"
                            v-model="item.sparePart"
                            label="Замена запчастей"
                            :items="spareParts"
                            item-text="NAME"
                            item-value="ID"
                            multiple
                            outlined
                            dense
                          ></v-autocomplete>
                          <v-autocomplete
                            :key="index + '/' + 'work'"
                            v-model="item.work"
                            label="Работы"
                            :items="works"
                            multiple
                            item-text="NAME"
                            item-value="ID"
                            outlined
                            dense
                          ></v-autocomplete>
                          <template v-if="item.obj.btns.length > 0">
                            <template v-for="(btn, index) in item.obj.btns">
                              <div
                                style="flex-direction: row; display: flex"
                                :key="
                                  index +
                                  '/' +
                                  item.obj.machineID +
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
                                    item.obj.machineID +
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
                                ></v-text-field>
                                <v-autocomplete
                                  :key="
                                    index +
                                    '/' +
                                    item.obj.machineID +
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
                                ></v-autocomplete>
                                <v-text-field
                                  :key="
                                    index +
                                    '/' +
                                    item.obj.machineID +
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
                          <template v-else>
                            <v-text-field
                              class="body-2"
                              v-model="item.obj.cups"
                              label="Общий счетчик"
                              outlined
                              dense
                            ></v-text-field>
                          </template>
                          <v-textarea
                            v-if="item.ShowComment === 'Y'"
                            class="body-2"
                            v-model="item.comment"
                            :label="item.commentLabel"
                            dense
                            outlined
                          ></v-textarea>
                          <!--<v-btn
                            class="ma-2 pa-2"
                            @click="addBtn(item)"
                            :key="
                              index + '/' + item.obj.machineID + '/' + 'btn'
                            "
                            small
                          >
                            Добавить кнопку с рецептом
                          </v-btn>-->
                        </template>
                        <v-row class="ma-1 pa-1 body-2">
                          <v-col v-if="item.StatusOkLabel">
                            <v-btn
                              :disabled="disabled"
                              x-large
                              color="light-green lighten-1"
                              class="button rounded-md white--text"
                              @click="closedProccess(item, 3)"
                            >
                              {{ item.StatusOkLabel }}
                            </v-btn>
                          </v-col>
                          <v-col v-if="item.StatusYesLabel">
                            <v-btn
                              :disabled="disabled"
                              x-large
                              color="light-green lighten-1"
                              class="button rounded-md white--text"
                              @click="closedProccess(item, 1)"
                            >
                              {{ item.StatusYesLabel.split(" ")[0] }} <br />
                              {{ item.StatusYesLabel.split(" ")[1] }}
                            </v-btn>
                          </v-col>
                          <v-col
                            v-if="item.StatusCancelLabel"
                            class="text-right"
                          >
                            <v-btn
                              :disabled="disabled"
                              x-large
                              color="red darken-3"
                              class="button rounded-md white--text"
                              @click="closedProccess(item, 4)"
                            >
                              {{ item.StatusCancelLabel.split(" ")[0] }} <br />
                              {{ item.StatusCancelLabel.split(" ")[1] }}
                            </v-btn>
                          </v-col>
                          <v-col v-if="item.StatusNoLabel" class="text-right">
                            <v-btn
                              :disabled="disabled"
                              x-large
                              color="red darken-3"
                              class="button rounded-md white--text"
                              @click="closedProccess(item, 2)"
                            >
                              {{ item.StatusNoLabel.split(" ")[0] }} <br />
                              {{ item.StatusNoLabel.split(" ")[1] }}
                            </v-btn>
                          </v-col>
                        </v-row>
                      </v-form>
                      <v-progress-circular
                        style="margin: 1rem"
                        v-else
                        :size="100"
                        indeterminate
                        color="green"
                      ></v-progress-circular>
                    </v-card-text>
                  </v-card>
                </div>
              </v-dialog>
            </template>
          </v-list-item-group>
        </v-list>
      </v-col>
    </v-row>
    <v-footer>
      <v-row align="center" class="ma-0">
        <v-col cols="6" offset="3" class="text-subtitle-2">
          <p>Процессов на стр.</p>
          {{ filterItems.length }}
        </v-col>
        <v-col cols="3">
          <v-select
            :items="pagesizes"
            v-model="filtersPagesize"
            @change="changePagesize"
            class="text-subtitle-2"
          ></v-select>
        </v-col>
      </v-row>
    </v-footer>
  </v-container>
</template>

<script>
import axios from "axios";
//import {BX24} from 'bx24';

export default {
  name: "Process-blank",
  data() {
    return {
      disabled: false,
      searchLoading: false,
      isDone: false,
      modal: false,
      items: [],
      machines: [],
      recipes: [],
      causes: [],
      spareParts: [],
      works: [],
      filterItems: [],
      pagesizes: ["5", "10", "15", "20", "Все"],
      search: "",
      filtersPagesize: "Все",
      size: null,
      photo1: "",
      photo2: "",
      photo3: "",
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
      //url: '',
      auth: "",
      domen: "",
    };
  },
  methods: {
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
    async getSpareParts() {
      try {
        const params = {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: 159,
          start: 0,
          auth: this.auth.ACCESS_TOKEN,
        };
        let isTrue = true;
        while (isTrue) {
          const list = await axios.post(this.url + "lists.element.get", params);
          this.spareParts.push(...list.data.result);
          if (list.data.next) params.start = list.data.next;
          else isTrue = false;
        }
      } catch (e) {
        console.log(e);
      }
    },
    async getWorks() {
      try {
        const params = {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: 157,
          start: 0,
          auth: this.auth.ACCESS_TOKEN,
        };
        let isTrue = true;
        while (isTrue) {
          const list = await axios.post(this.url + "lists.element.get", params);
          this.works.push(...list.data.result);
          if (list.data.next) params.start = list.data.next;
          else isTrue = false;
        }
      } catch (e) {
        console.log(e);
      }
    },
    async getCauses() {
      try {
        const params = {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: 161,
          start: 0,
          auth: this.auth.ACCESS_TOKEN,
        };
        let isTrue = true;
        while (isTrue) {
          const list = await axios.post(this.url + "lists.element.get", params);
          this.causes.push(...list.data.result);
          if (list.data.next) params.start = list.data.next;
          else isTrue = false;
        }
      } catch (e) {
        console.log(e);
      }
    },
    async processOpen(item) {
      try {
        await this.getObj(item);
        item.proccessCard = true;
      } catch (e) {
        console.log(e);
      }
    },
    async getObj(item) {
      console.log({ item });
      const objParams = {
        IBLOCK_TYPE_ID: "lists",
        IBLOCK_ID: "151",
        FILTER: {
          PROPERTY_605: item.companyID,
        },
        auth: this.auth.ACCESS_TOKEN,
      };
      const objData = await axios.post(
        this.url + "lists.element.get",
        objParams
      );
      console.log({ objData });
      if (objData.data.result.length > 0) {
        const obj = {
          objID: objData.data.result[0].ID,
          machineID: Object.values(objData.data.result[0].PROPERTY_607)[0],
          btns: [],
          cups: 0,
          newObjID: "",
          machineSerial: "",
          machineModel: "",
          machineBrand: "",
          disabled: true,
        };
        await this.getMachine(obj);
        item.obj = obj;
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

        this.machines.push(selectMachine.data.result[0]);

        const paramsBtn = {
          IBLOCK_TYPE_ID: "lists",
          IBLOCK_ID: 153,
          FILTER: {
            PROPERTY_615: item.machineID,
          },
          start: 0,
          auth: this.auth.ACCESS_TOKEN,
        };
        const btns = [];
        const listBtn = await axios.post(
          this.url + "lists.element.get",
          paramsBtn
        );
        btns.push(...listBtn.data.result);

        console.log({ btns });
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
      } catch (e) {
        console.log(e);
      }
    },
    changePagesize() {
      try {
        if (this.filtersPagesize === "Все") {
          this.filterItems = this.items;
        } else {
          this.filterItems = this.items.filter((item, index) => {
            return index < this.filtersPagesize;
          });
        }
      } catch (e) {
        console.log(e);
      }
    },
    searchItems() {
      if (this.search.length > 2) {
        this.filterItems = this.items.filter((item) => {
          return (
            item.name.toLowerCase().indexOf(this.search.toLowerCase()) > -1 ||
            item.description.toLowerCase().indexOf(this.search.toLowerCase()) >
              -1
          );
        });
        // console.log(this.items)
      } else {
        this.filterItems = this.items;
      }
    },
    async getProccess() {
      try {
        const user = await axios.post(this.url + "profile", {
          auth: this.auth.ACCESS_TOKEN,
        });
        const methods = "bizproc.task.list";
        let start = 0;
        let i = 0;
        const items = [];

        while (i == 0) {
          const paramsItem = {
            select: ["ID", "DESCRIPTION", "NAME", "PARAMETERS"],
            order: { ID: "DESC" },
            filter: { STATUS: 0, USER_ID: user.data.result.ID },
            start: start,
            auth: this.auth.ACCESS_TOKEN,
          };
          console.log({ paramsItem });
          const itemsData = await axios.post(this.url + methods, paramsItem);
          console.log({ itemsData });
          for (const elem of itemsData.data.result) {
            console.log({ elem });
            if (elem.NAME.includes("Ремонт")) items.push(elem);
          }
          if (itemsData.data.next) {
            start = itemsData.data.next;
          } else {
            i = 1;
          }
        }

        for (const item of items) {
          console.log({ item });
          const proccessData = await axios.post(
            this.url + "lists.element.get",
            {
              IBLOCK_TYPE_ID: "bitrix_processes",
              IBLOCK_ID: 71,
              ELEMENT_ID: item.DOCUMENT_ID,
              auth: this.auth.ACCESS_TOKEN,
            }
          );
          console.log({ proccessData });
          const description = item.DESCRIPTION.split("\n");
          const proccess = {
            id: item.ID,
            name: item.NAME,
            cause: "",
            sparePart: "",
            work: "",
            description: item.DESCRIPTION,
            descriptionRows: [],
            proccessCard: false,
            documentUrl: item.PARAMETERS.DOCUMENT_URL,
            commentLabel: item.PARAMETERS.CommentLabel,
            commentRequired: item.PARAMETERS.CommentRequired,
            comment: "",
            companyID: Object.values(
              proccessData.data.result[0].PROPERTY_275
            )[0],
            obj: {
              id: "",
              ibjID: "",
              machineID: "",
              machineBrand: "",
              machineModel: "",
              machineSerial: "",
              btns: "",
              photo1: "",
              photo2: "",
              photo3: "",
            },
            disabled: true,
            ShowComment: item.PARAMETERS.ShowComment,
            show: false,
          };
          for (const row of description) {
            proccess.descriptionRows.push({
              type: "text",
              value: row.replace(/&nbsp;/gi, " ").replace(/&#8381;/gi, "руб."),
            });
          }
          console.log(proccess.descriptionRows, "rows");

          if (item.PARAMETERS.StatusCancelLabel)
            proccess.StatusCancelLabel = item.PARAMETERS.StatusCancelLabel;
          if (item.PARAMETERS.StatusOkLabel)
            proccess.StatusOkLabel = item.PARAMETERS.StatusOkLabel;
          if (item.PARAMETERS.StatusNoLabel)
            proccess.StatusNoLabel = item.PARAMETERS.StatusNoLabel;
          if (item.PARAMETERS.StatusYesLabel)
            proccess.StatusYesLabel = item.PARAMETERS.StatusYesLabel;
          this.items.push(proccess);
        }
        // console.log(this.items, 'items')
        this.filterItems = this.items;
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

        // const diskID = await axios.post(this.url + 'disk.storage.getforapp', {})
        //console.log({diskID})
        const fileID = await axios.post(this.url + "disk.folder.uploadfile", {
          id: 336771, //diskID[0].ID,
          data: { NAME: file.name },
          fileContent: [file.name, await getBase64(file)],
          generateUniqueName: "Y",
          auth: this.auth.ACCESS_TOKEN,
        });

        console.log({ fileID });

        switch (type) {
          case 1:
            this.photo1 = ["n" + fileID.data.result.ID];
            break;
          case 2:
            this.photo2 = ["n" + fileID.data.result.ID];
            break;
          case 3:
            this.photo3 = ["n" + fileID.data.result.ID];
            break;
        }
      } catch (e) {
        console.log(e);
      }
    },
    async closedProccess(item, status) {
      try {
        console.log({ item });
        console.log({ status });
        this.isDone = true;
        let isAlert = false;
        const isFields = {};
        const date = Date.now();
        if (item.StatusOkLabel != "Взять в работу") {
          if (item.commentRequired == "Y") {
            if (item.comment.length <= 0) {
              alert("Вы не заполнили комментарий!");
              isAlert = true;
            }
          }
          //TODO проставить обязательность полей
          if (!item.cause) {
            alert("Вы не заполнили причины!");
            isAlert = true;
          } else {
            isFields.whatList = item.cause;
          }

          if (!item.sparePart) {
            alert("Вы не заполнили запчасти!");
            isAlert = true;
          } else {
            isFields.itemsPart = item.sparePart;
          }

          if (!item.work) {
            alert("Вы не заполнили работы!");
            isAlert = true;
          } else {
            isFields.worksPart = item.work;
          }

          if (!item.obj.photo1 || !item.obj.photo2 || !item.obj.photo3) {
            alert("Вы не прикрепили фото!");
            isAlert = true;
          }

          isFields.check = 0;
          if (!isAlert) {
            console.log(item, "item");
            await this.addPhoto(1, item.obj.photo1);
            await this.addPhoto(2, item.obj.photo2);
            await this.addPhoto(3, item.obj.photo3);
            //создаем лог фото
            const paramsLog = {
              IBLOCK_TYPE_ID: "lists",
              IBLOCK_ID: "149",
              ELEMENT_CODE: `log_${date}`,
              FIELDS: {
                NAME: "Сервис",
                PROPERTY_621: item.companyID,
                PROPERTY_599: item.obj.machineID,
                PROPERTY_603: "Сервис",
                PROPERTY_601: "",
                PROPERTY_631: "",
                PROPERTY_633: { n0: this.photo1 },
                PROPERTY_635: { n0: this.photo2 },
                PROPERTY_637: { n0: this.photo3 },
              },
              auth: this.auth.ACCESS_TOKEN,
            };
            console.log({ paramsLog });
            await axios.post(this.url + "lists.element.add", paramsLog);
            //создаем логи чашек
            if (item.obj.btns.length > 0) {
              const batchParams = {
                halt: 0,
                cmd: {},
                auth: this.auth.ACCESS_TOKEN,
              };
              for (const btnIndex in item.obj.btns) {
                isFields.check += Number(item.obj.btns[btnIndex].cups);
                batchParams.cmd[
                  `btn_${btnIndex}`
                ] = `lists.element.update?IBLOCK_TYPE_ID=lists&IBLOCK_ID=153&ELEMENT_ID=${item.obj.btns[btnIndex].id}&FIELDS[PROPERTY_609]=${item.obj.btns[btnIndex].number}&FIELDS[PROPERTY_611]=${item.obj.btns[btnIndex].recipeID}&FIELDS[PROPERTY_613]=${item.obj.btns[btnIndex].cups}&FIELDS[PROPERTY_623]=${item.obj.btns[btnIndex].oldCups}&FIELDS[PROPERTY_615]=${item.obj.machineID}&FIELDS[NAME]=Кнопка ${item.obj.btns[btnIndex].number}`;
                batchParams.cmd[
                  `log_${btnIndex}`
                ] = `lists.element.add?IBLOCK_TYPE_ID=lists&IBLOCK_ID=155&ELEMENT_CODE=log_${date}/log_${btnIndex}&FIELDS[NAME]=Лог&FIELDS[PROPERTY_625]=${item.obj.machineID}&FIELDS[PROPERTY_627]=${item.obj.btns[btnIndex].id}&FIELDS[PROPERTY_629]=${item.obj.btns[btnIndex].cups}`;
                if (
                  btnIndex > 49 ||
                  btnIndex == Number(item.obj.btns.length) - 1
                ) {
                  console.log({ batchParams });
                  const batchResult = await axios.post(
                    this.url + "batch",
                    batchParams
                  );
                  console.log({ batchResult });
                  batchParams.cmd = {};
                }
                item.obj.btns[btnIndex].oldCups = item.obj.btns[btnIndex].cups;
              }
            } else {
              isFields.check = Number(item.obj.cups);
            }

            //закрываем задачу
            const params = {
              TASK_ID: item.id,
              STATUS: status,
              COMMENT: item.comment,
              Fields: isFields,
              auth: this.auth.ACCESS_TOKEN,
            };
            const close = await axios.post(
              this.url + "bizproc.task.complete",
              params
            );
            console.log({ close });
            const index = this.items.indexOf(item);
            this.items.splice(index, 1);
          }
          item.proccessCard = false;
          this.isDone = false;
        } else {
          const params = {
            TASK_ID: item.id,
            STATUS: status,
            COMMENT: item.comment,
            auth: this.auth.ACCESS_TOKEN,
          };
          const close = await axios.post(
            this.url + "bizproc.task.complete",
            params
          );
          console.log({ close });
          const index = this.items.indexOf(item);
          this.items.splice(index, 1);
          item.proccessCard = false;
          this.isDone = false;
        }
      } catch (error) {
        console.log({ error });
      }
    },
  },
  async mounted() {
    //const bx24 = new BX24();
    //this.auth = await bx24.getAuth()
   // this.url = `https://${this.auth.DOMAIN}/rest/`
   // this.domen = this.auth.DOMAIN

    this.getRecipes();
    this.getSpareParts();
    this.getWorks();
    this.getCauses();
    await this.getProccess();
    await this.changePagesize();
  },
};
</script>

<style scoped></style>
