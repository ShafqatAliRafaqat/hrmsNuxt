<template>
  <v-container
    id="user-profile"
    fluid
    tag="section"
  >
    <v-row justify="center">
      <v-col
        cols="12"
        md="12"
      >
        <MaterialCard
          color="success"
          title="Letter"
          class="px-5 py-3"
        >
          <v-data-table
            :headers="headers"
            :items="allData"
            sort-by="en_name"
          >
            <template v-slot:top>
              <v-toolbar
                flat
              >
                <v-spacer></v-spacer>
                <v-dialog
                  v-model="dialog"
                  max-width="500px"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-btn
                      color="primary"
                      dark
                      class="mb-2"
                      v-bind="attrs"
                      v-on="on"
                      @click="reset"
                      rounded
                    >
                      Add Letter
                    </v-btn>
                  </template>
                  <v-card>
                    <v-card-title>
                      <span class="headline">{{ formTitle }}</span>
                    </v-card-title>
                    <v-card-text>
                      <v-container>
                        <v-form ref="form">
                          <v-container class="py-0">
                            <v-row>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-select
                                  v-model="editedItem.company_id"
                                  :items="companies"
                                  :item-text="companies.text"
                                  :item-value="companies.value"
                                  label="Select Company"
                                ></v-select>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-select
                                  v-model="editedItem.branch_id"
                                  :items="branches"
                                  :item-text="branches.text"
                                  :item-value="branches.value"
                                  label="Select branch"
                                ></v-select>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-text-field
                                  label="Serial id"
                                  type="number"
                                  v-model="editedItem.serial_id"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-text-field
                                  label="Doc type"
                                  type="number"
                                  v-model="editedItem.doc_type"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-checkbox
                                  v-model="editedItem.request"
                                  :false-value="0"
                                  :true-value="1"
                                  label="Request"
                                  color="success"
                                  hide-details
                                ></v-checkbox>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-text-field
                                  label="Name in Arabic"
                                  class="direction"
                                  v-model="editedItem.ar_name"
                                  :rules="[ (value) => !!value || 'This  field is required',
                                (value) => (value && value.length <= 50) || 'maximum 50 characters',]"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-text-field
                                  label="Name in English"
                                  v-model="editedItem.en_name"
                                  :rules="[ (value) => !!value || 'This  field is required',
                                (value) => (value && value.length <= 50) || 'maximum 50 characters',]"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-text-field
                                  label="Description in Arabic"
                                  class="direction"
                                  v-model="editedItem.ar_description"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-text-field
                                  label="Description in English"
                                  v-model="editedItem.en_description"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="6"
                              >
                                <v-text-field
                                  label="Language"
                                  v-model="editedItem.language"
                                ></v-text-field>
                              </v-col>
                            </v-row>
                          </v-container>
                        </v-form>

                      </v-container>
                    </v-card-text>

                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn
                        color="blue darken-1"
                        text
                        @click="dialog = false"
                        rounded
                      >
                        Cancel
                      </v-btn>
                      <v-btn
                        color="blue darken-1"
                        text
                        @click="save"
                        rounded
                      >
                        Save
                      </v-btn>
                    </v-card-actions>
                  </v-card>
                </v-dialog>
                <v-dialog v-model="dialogDelete" max-width="390px" persistent>
                  <v-card>
                    <v-card-title class="headline">Are you sure you want to delete this record?</v-card-title>
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn color="blue darken-1" text @click="dialogDelete=false">Cancel</v-btn>
                      <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
                      <v-spacer></v-spacer>
                    </v-card-actions>
                  </v-card>
                </v-dialog>


<!--                Letter fields model-->
                <v-dialog v-model="dialogAttach" max-width="unset" persistent>
                  <v-card>
                    <v-card-title class="headline">Attach Letter Fields</v-card-title>
                    <v-card-text>
                      <v-container>
                        <v-form ref="form">
                          <v-container class="py-0">
                            <v-row>
                              <v-col
                                cols="12"
                                sm="6"
                                md="2"
                              >
                                <v-select
                                  v-model="attachData.column_id"
                                  :items="column"
                                  :item-text="column.text"
                                  :item-value="column.value"
                                  label="Select Column"
                                ></v-select>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="2"
                              >
                                <v-text-field
                                  label="Order by"
                                  type="number"
                                  v-model="attachData.order_by"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="2"
                              >
                                <v-text-field
                                  label="An sequence"
                                  type="number"
                                  v-model="attachData.ar_sequence"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="2"
                              >
                                <v-text-field
                                  label="Format"
                                  v-model="attachData.format"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="2"
                              >
                                <v-checkbox
                                  v-model="attachData.both_language"
                                  :false-value="0"
                                  :true-value="1"
                                  label="Both language"
                                  color="success"
                                  hide-details
                                ></v-checkbox>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="2"
                              >
                                <v-text-field
                                  label="En sequence"
                                  type="number"
                                  v-model="attachData.en_sequence"
                                ></v-text-field>
                              </v-col>
                              <v-col
                                cols="12"
                                sm="6"
                                md="1"
                                offset-md="5"
                              >
                                <v-btn color="blue darken-1" text @click="attachWithLetter">Attach</v-btn>
                              </v-col>
                            </v-row>
                          </v-container>
                        </v-form>
                      </v-container>
                      <v-data-table
                        :headers="attachHeaders"
                        :items="letterFieldsData"
                        sort-by="en_name"
                      >
                        <template v-slot:item.actions="{ item }">
                          <v-icon
                            small
                            @click="deleteItem(item.id)"
                          >
                            mdi-delete
                          </v-icon>
                        </template>

                      </v-data-table>
                    </v-card-text>
                    <v-card-actions>
                      <v-spacer></v-spacer>
                      <v-btn color="blue darken-1" text @click="dialogAttach=false">Cancel</v-btn>
<!--                      <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>-->
                      <v-spacer></v-spacer>
                    </v-card-actions>
                  </v-card>
                </v-dialog>


              </v-toolbar>
            </template>

            <template v-slot:item.actions="{ item }">
              <v-icon
                small
                class="mr-2"
                @click="attachFields(item.id)"
              >
                mdi-attachment
              </v-icon>
              <v-icon
                small
                class="mr-2"
                @click="editItem(item)"
              >
                mdi-pencil
              </v-icon>
              <v-icon
                small
                @click="deleteItem(item.id)"
              >
                mdi-delete
              </v-icon>
            </template>
          </v-data-table>
        </MaterialCard>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
import MaterialCard from "../../../components/base/MaterialCard";
import Vue from "vue";

export default {
  name: "Letter.vue",
  components: {MaterialCard },
  middleware: ["auth"],
  data(){
    return{
      dialog: false,
      dialogDelete: false,
      dialogAttach: false,
      headers: [
        {
          text: 'ID',
          align: 'start',
          value: 'id',
        },
        { text: 'Serial id', value: 'serial_id' },
        { text: 'Doc type', value: 'doc_type' },
        { text: 'Request', value: 'request' },
        { text: 'En name', value: 'en_name' },
        { text: 'Ar name', value: 'ar_name' },
        { text: 'Ar description', value: 'ar_description' },
        { text: 'En description', value: 'en_description' },
        { text: 'Language', value: 'language' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      attachHeaders: [
        {
          text: 'ID',
          align: 'start',
          value: 'id',
        },
        { text: 'Order by', value: 'order_by' },
        { text: 'Ar sequence', value: 'ar_sequence' },
        { text: 'En sequence', value: 'en_sequence' },
        { text: 'Both language', value: 'both_language' },
        { text: 'Format', value: 'format' },
        { text: 'Actions', value: 'actions', sortable: false },
      ],
      desserts: [],
      editedIndex: -1,
      editedItem: {
        company_id: '',
        branch_id: '',
        serial_id: '',
        doc_type: '',
        en_name: '',
        ar_name: '',
        en_description: '',
        ar_description: '',
        language: '',
        request: '0',
      },
      attachData :{
        column_id : '',
        letter_id: '',
        order_by: '',
        ar_sequence: '',
        en_sequence: '',
        both_language: '0',
        format: '',
        language: ''
      },
      countryId:[],
      allData: [],
      companies: [],
      branches: [],
      column: [],
      letterFieldsData: [],
    }
  },
  computed: {
    formTitle () {
      return this.editedIndex === -1 ? 'New Letter' : 'Edit Letter'
    }
  },
  created () {
    this.getColumn()
    this.getCompanies()
    this.getBranches()
    this.getList()
  },
  methods: {
    getColumn () {
      let arr = []
      let data = { path: "/column_selects?group_by=1" }
      this.$store.dispatch('list',data).then(response => {
        if(Array.isArray(response.data.data)) {
          response.data.data.forEach(data => {
            arr.push({
              text : data.en_description +' '+ data.ar_description,
              value: data.id
            })
          })
        } else {
          arr.push({
            text : response.data.data.en_description +' '+ response.data.data.ar_description,
            value: response.data.data.id
          })
        }
        this.column = arr
      })
    },
    getCompanies () {
      let arr = []
      let data = { path: "/companies" }
      this.$store.dispatch('list',data).then(response => {
        response.data.data.forEach(data => {
          arr.push({
            text : data.en_name +' '+ data.ar_name,
            value: data.id
          })
        })
        this.companies = arr
      })
    },
    getBranches () {
      let arr = []
      let data = { path: "/company_branches" }
      this.$store.dispatch('list',data).then(response => {
        response.data.data.forEach(data => {
          arr.push({
            text : data.en_name +' '+ data.ar_name,
            value: data.id
          })
        })
        this.branches = arr
      })
    },
    getLetterFields () {
      let data = { path: "/letter_fields" }
      this.$store.dispatch('list',data).then(response => {
        this.letterFieldsData = response.data.data
        this.$store.commit("SHOW_LOADER", false);
        this.$store.commit("SHOW_SNACKBAR", {
          snackbar: true,
          color: "green",
          message: response.data.message
        })
      })
    },
    getList(){
      let data = { path: "/letters" }
      this.$store.dispatch('list',data).then(response => {
        this.allData = response.data.data
        this.$store.commit("SHOW_LOADER", false);
        this.$store.commit("SHOW_SNACKBAR", {
          snackbar: true,
          color: "green",
          message: response.data.message
        });
      });
    },
    async save () {
      if(this.$refs.form.validate()) {
        if (this.editedIndex > -1) {
          let data={
            path:"/letter/"+this.editedItem.id,
            data:this.editedItem
          }
          this.dialog = false
          this.$store.commit("SHOW_LOADER", true);
          await this.$store.dispatch("update", data).then(response => {
            this.$store.commit("SHOW_LOADER", false);
            this.$store.commit("SHOW_SNACKBAR", {
              snackbar: true,
              color: "green",
              message: response.data.message
            });
            this.getList()
          }).catch(error => {
            this.$store.commit("SHOW_LOADER", false);
            this.$store.commit("SHOW_SNACKBAR", {
              snackbar: true,
              color: "error",
              message: error.response.data.message
            });
          })
        }
        else {
          let data={
            path:"/letters",
            data:this.editedItem
          }
          this.dialog = false
          this.$store.commit("SHOW_LOADER", true);
          await this.$store.dispatch("create", data).then(response => {
            this.$store.commit("SHOW_LOADER", false);
            this.$store.commit("SHOW_SNACKBAR", {
              snackbar: true,
              color: "green",
              message: response.data.message
            });
            this.getList()
          }).catch(error => {
            this.$store.commit("SHOW_LOADER", false);
            this.$store.commit("SHOW_SNACKBAR", {
              snackbar: true,
              color: "error",
              message: error.response.data.message
            });
          })
        }
      }

    },
    editItem (item) {
      this.editedIndex = 2
      // this.editedIndex =this.desserts.indexOf(item)
      // console.log('index',this.desserts.indexOf(item))
      this.editedItem = Vue.util.extend({}, item);
      this.editedItem.company_id = item.company_id.id
      this.editedItem.branch_id = item.branch_id.id
      this.dialog = true
    },
    deleteItem (id) {
      this.countryId[0]=id
      // this.editedIndex = this.desserts.indexOf(item)
      // this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },
    async deleteItemConfirm() {
      this.dialogDelete = false
      let path = this.dialogAttach === true ? '/delete_letter_fields' : '/delete_letters'
      this.$store.commit("SHOW_LOADER", true);
      let data = {
        'ids': this.countryId,
        'path' : path
      }
      await this.$store.dispatch("delete", data).then(response => {
        this.$store.commit("SHOW_LOADER", false);
        this.$store.commit("SHOW_SNACKBAR", {
          snackbar: true,
          color: "green",
          message: response.data.message
        });
        this.getList()
        this.getLetterFields()
      }).catch(error => {
        this.$store.commit("SHOW_LOADER", false);
        this.$store.commit("SHOW_SNACKBAR", {
          snackbar: true,
          color: "error",
          message: error.response.data.message
        });
      })
    },
    reset() {
      this.editedItem.company_id = ''
      this.editedItem.branch_id = ''
      this.editedItem.serial_id = ''
      this.editedItem.doc_type = ''
      this.editedItem.en_name = ''
      this.editedItem.ar_name = ''
      this.editedItem.en_description = ''
      this.editedItem.ar_description = ''
      this.editedItem.language = ''
      this.editedItem.request = '0'
      this.countryId = []
      this.editedIndex = -1
    },
    attachFields(id){
      this.getLetterFields()
      this.dialogAttach = true
      this.attachData.letter_id = id
    },
    attachWithLetter () {
      let data = {
        path:"/letter_fields",
        data:this.attachData
      }
      this.$store.commit("SHOW_LOADER", true);
        this.$store.dispatch("create", data).then(response => {
        this.resetAttachData()
        this.$store.commit("SHOW_LOADER", false);
        this.$store.commit("SHOW_SNACKBAR", {
          snackbar: true,
          color: "green",
          message: response.data.message
        });
        this.getLetterFields()
      }).catch(error => {
        this.$store.commit("SHOW_LOADER", false);
        this.$store.commit("SHOW_SNACKBAR", {
          snackbar: true,
          color: "error",
          message: error.response.data.message
        });
      })
    },
    resetAttachData () {
      this.attachData.column_id  = ''
      this.attachData.letter_id = ''
      this.attachData.order_by = ''
      this.attachData.ar_sequence = ''
      this.attachData.en_sequence = ''
      this.attachData.both_language = '0'
      this.attachData.format = ''
      this.attachData.language = ''
    }
  }
}
</script>

<style scoped>

</style>
