<template>
  <div>
    <!-- Judul tabel scheduler -->
    <v-toolbar>
      <v-toolbar-title>List Of Scheduler</v-toolbar-title>
      <v-divider class="mx-2" inset vertical></v-divider>
      <v-spacer></v-spacer>

      <!-- Select category component -->
      <v-layout row wrap align-center>
        <v-flex xs3>
          <v-subheader>Select Category</v-subheader>
        </v-flex>
        <v-flex xs3>
          <v-select v-model="selectedCategory" :items="optionCategory" hide-details single-line></v-select>
        </v-flex>
      </v-layout>
      <v-spacer></v-spacer>

      <!-- Dialog buat baru dan edit scheduler -->
      <v-dialog v-model="dialog" persistent max-width="500px">
        <!-- Tombol buat baru -->
        <template v-slot:activator="{ on }">
          <v-btn color="primary" dark class="mb-2" v-on="on">Create</v-btn>
        </template>

        <!-- Isi Dialog -->
        <v-card>
          <!-- Judul Dialog -->
          <v-card-title>
            <span class="headline">{{ formTitle }}</span>
          </v-card-title>

          <!-- Body dialog berisi form untuk buat dan edit -->
          <v-card-text >
            <v-container grid-list-md v-if="method === 'edit' || method === 'new'">
              <v-layout wrap>
                <v-form ref="form" v-model="valid" lazy-validation>
                <v-flex xs12>
                  <v-text-field v-model="editedItem.device_name" label="Device name" required :rules="nameRules"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-select v-model="editedItem.category" :items="optionCategory" required :rules="categoryRules"></v-select>
                </v-flex>
                <v-flex xs12>
                  <v-menu
                    ref="menu"
                    v-model="menu"
                    :close-on-content-click="false"
                    :nudge-right="40"
                    :return-value.sync="editedItem.time"
                    lazy
                    transition="scale-transition"
                    offset-y
                    full-width
                    max-width="290px"
                    min-width="290px"
                  >
                    <template v-slot:activator="{ on }">
                      <v-text-field
                        v-model="editedItem.time"
                        label="Set time"
                        prepend-icon="access_time"
                        readonly
                        v-on="on"
                      ></v-text-field>
                    </template>
                    <v-time-picker
                      v-if="menu"
                      v-model="editedItem.time"
                      full-width
                      format="24hr"
                      @click:minute="$refs.menu.save(editedItem.time)"
                    ></v-time-picker>
                  </v-menu>
                </v-flex>
                <v-flex xs12 v-if="editedItem.category === 'Weekly'">
                  <v-layout wrap>
                    <v-flex xs4>
                      <v-checkbox
                        v-model="editedItem.day"
                        label="Sunday"
                        value="Sunday"
                        color="success"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs4>
                      <v-checkbox
                        v-model="editedItem.day"
                        label="Monday"
                        value="Monday"
                        color="success"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs4>
                      <v-checkbox
                        v-model="editedItem.day"
                        label="Tuesday"
                        value="Tuesday"
                        color="success"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs4>
                      <v-checkbox
                        v-model="editedItem.day"
                        label="Wednesday"
                        value="Wednesday"
                        color="success"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs4>
                      <v-checkbox
                        v-model="editedItem.day"
                        label="Thursday"
                        value="Thursday"
                        color="success"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs4>
                      <v-checkbox
                        v-model="editedItem.day"
                        label="Friday"
                        value="Friday"
                        color="success"
                      ></v-checkbox>
                    </v-flex>
                    <v-flex xs4>
                      <v-checkbox
                        v-model="editedItem.day"
                        label="Saturday"
                        value="Saturday"
                        color="success"
                      ></v-checkbox>
                    </v-flex>
                  </v-layout>
                </v-flex>
                <v-flex xs12 v-else-if="editedItem.category === 'Monthly'">
                  <v-menu
                    ref="menu1"
                    v-model="menu1"
                    :close-on-content-click="false"
                    :nudge-right="40"
                    :return-value.sync="editedItem.date"
                    lazy
                    transition="scale-transition"
                    offset-y
                    full-width
                    min-width="290px"
                  >
                    <template v-slot:activator="{ on }">
                      <v-text-field
                        v-model="editedItem.date"
                        label="Picker in menu"
                        prepend-icon="event"
                        readonly
                        v-on="on"
                      ></v-text-field>
                    </template>
                    <v-date-picker v-model="editedItem.date" no-title scrollable>
                      <v-spacer></v-spacer>
                      <v-btn flat color="primary" @click="menu1 = false">Cancel</v-btn>
                      <v-btn flat color="primary" @click="$refs.menu1.save(editedItem.date)">OK</v-btn>
                    </v-date-picker>
                  </v-menu>
                </v-flex>
                <v-flex xs12>
                  <v-switch
                    v-model="editedItem.activation"
                    true-value="1"
                    false-value="0"
                    label="Activation"
                    color="success"
                  ></v-switch>
                </v-flex>
                </v-form>
              </v-layout>
            </v-container>

              <!-- Body untuk detail -->
              <v-container grid-list-md v-else>
              <v-layout wrap>
                <v-flex xs12>
                  <v-layout>
                    <v-flex xs4>Device Name</v-flex>
                    <v-flex xs2>:</v-flex>
                    <v-flex xs4>{{editedItem.device_name}}</v-flex>
                  </v-layout>
                </v-flex>
                <v-flex xs12>
                  <v-layout>
                    <v-flex xs4>Category</v-flex>
                    <v-flex xs2>:</v-flex>
                    <v-flex xs4>{{editedItem.category}}</v-flex>
                  </v-layout>
                </v-flex>
                <v-flex xs12>
                  <v-layout>
                    <v-flex xs4>Time</v-flex>
                    <v-flex xs2>:</v-flex>
                    <v-flex xs4>{{editedItem.time}}</v-flex>
                  </v-layout>
                </v-flex>
                <v-flex xs12 v-if="editedItem.category === 'Weekly'">
                  <v-layout>
                    <v-flex xs4>Days</v-flex>
                    <v-flex xs2>:</v-flex>
                    <v-flex xs4>
                    <span v-for="(item,index) in editedItem.day" :key="index">{{item}}, </span> 
                    </v-flex>
                  </v-layout>
                </v-flex>
                <v-flex xs12 v-else-if="editedItem.category === 'Monthly'">
                  <v-layout>
                    <v-flex xs4>Start Date</v-flex>
                    <v-flex xs2>:</v-flex>
                    <v-flex xs4>{{editedItem.date}}</v-flex>
                  </v-layout>
                </v-flex>
                <v-flex xs12>
                  <v-layout>
                    <v-flex xs4>Activation</v-flex>
                    <v-flex xs2>:</v-flex>
                    <v-flex xs4>
                      <span color="success" v-if="editedItem.activation === '1'">ON</span>
                      <span color="error" v-else-if="editedItem.activation === '0'">OFF</span>
                    </v-flex>
                  </v-layout>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>

          <!-- Button action edit, buat, cancel, ok -->
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" flat @click="close" v-if="method === 'edit' || method === 'new'">Cancel</v-btn>
            <v-btn color="blue darken-1" flat @click="save" v-if="method === 'edit' || method === 'new'">Save</v-btn>
            <v-btn color="blue darken-1" flat @click="close1" v-else>OK</v-btn>
          </v-card-actions>

        </v-card>
      </v-dialog>
    </v-toolbar>

    <!-- Tabel daftar scheduler -->
    <v-data-table
      :headers="headers"
      :items="desserts"
      :search="selectedCategory"
      :loading="loading"
      class="elevation-1"
    >
      <template v-slot:items="props">
        <td class="text-xs-left">{{ props.index + 1}}</td>
        <td class="text-xs-left">{{ props.item.device_name }}</td>
        <td class="text-xs-left">{{ props.item.category }}</td>

        <td class="text-xs-left">
          <v-btn color="success" v-if="props.item.activation === '1'">ON</v-btn>
          <v-btn color="error" v-else-if="props.item.activation === '0'">OFF</v-btn>
        </td>
        <td class="text-xs-left">
          <v-icon class="mr-2" @click="detailItem(props.item)" color="primary">info</v-icon>
          <v-icon class="mr-2" @click="editItem(props.item)" color="success">edit</v-icon>
          <v-icon @click="deleteItem(props.item)" color="error">delete</v-icon>
        </td>
      </template>
      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize">Reset</v-btn>
      </template>
    </v-data-table>
  </div>
</template>

<script>
export default {
  name: "HelloWorld",
  data: () => ({
    valid: true,
    menu1: false,
    menu: false,
    loading: false,
    method: "new",
    selectedCategory: null,
    dialog: false,
    headers: [
      {
        text: "No",
        sortable: false
      },
      { text: "Device Name", value: "device_name", sortable: false },
      { text: "Category", value: "category", sortable: false },
      { text: "Activation", value: "activation", sortable: false },
      { text: "Actions", value: "device_name", sortable: false }
    ],
    desserts: [],
    editedIndex: -1,
    editedItem: {
      device_name: "",
      category: null,
      time: "",
      day: [],
      date: "",
      activation: "1"
    },
    defaultItem: {
      device_name: "",
      category: null,
      time: "",
      day: [],
      date: "",
      activation: "1"
    },
    optionCategory: [
      { text: "Select", value: null },
      { text: "Daily", value: "Daily" },
      { text: "Weekly", value: "Weekly" },
      { text: "Monthly", value: "Monthly" }
    ],
    nameRules: [
      v => !!v || 'Device name is required',
    ],
    categoryRules: [
      v => !!v || 'Category is required',
    ]
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1
        ? "Create New Scheduler "
        : `Edit ${this.editedItem.device_name}`;
    }
  },

  watch: {
    dialog(val) {
      val || this.close();
    }
  },

  mounted() {
    this.loading = true;

    setTimeout(() => {
      this.loading = false;
      this.initialize();
    }, 3000);
  },

  methods: {
    initialize() {
      this.desserts = [
        {
          device_name: "Device 1",
          category: "Daily",
          time: "08:00",
          activation: "1"
        },
        {
          device_name: "Device 2",
          category: "Weekly",
          day: ["Sunday", "Monday"],
          time: "08:00",
          activation: "1"
        },
        {
          device_name: "Device 3",
          category: "Monthly",
          date: "2019-07-01",
          time: "08:00",
          activation: "1"
        },
        {
          device_name: "Device 4",
          category: "Daily",
          time: "08:00",
          activation: "0"
        },
        {
          device_name: "Device 5",
          category: "Daily",
          time: "08:00",
          activation: "1"
        },
        {
          device_name: "Device 6",
          category: "Weekly",
          day: ["Sunday", "Monday"],
          time: "08:00",
          activation: "0"
        },
        {
          device_name: "Device 7",
          category: "Monthly",
          date: "2019-07-15",
          time: "08:00",
          activation: "0"
        }
      ];
    },

    editItem(item) {
      this.method = "edit";
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },
    detailItem(item) {
      this.method = "view";
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = true;
    },

    deleteItem(item) {
      const index = this.desserts.indexOf(item);
      confirm("Are you sure you want to delete this item?") &&
        this.desserts.splice(index, 1);
    },

    close() {
      this.dialog = false;
     
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
        this.method = "new";
      }, 300);
    },
    close1() {
      this.dialog = false;
      
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
        this.method = "new";
      }, 300);
    },
    validate () {
        if (this.$refs.form.validate()) {
          this.snackbar = true
        }
    },
    resetValidation () {
        this.$refs.form.resetValidation()
    },
    save() {
      if (this.editedIndex > -1) {
        Object.assign(this.desserts[this.editedIndex], this.editedItem);
      } else if (this.editedItem.category === "Daily"){
        this.desserts.push({
          device_name: this.editedItem.device_name,
          category: this.editedItem.category,
          time: this.editedItem.time,
          activation: this.editedItem.activation
        });
      } else if(this.editedItem.category === "Weekly") {
        this.desserts.push({
          device_name: this.editedItem.device_name,
          category: this.editedItem.category,
          day: this.editedItem.day,
          time: this.editedItem.time,
          activation: this.editedItem.activation
        });
      } else if(this.editedItem.category === "Monthly") {
        this.desserts.push({
          device_name: this.editedItem.device_name,
          category: this.editedItem.category,
          date: this.editedItem.date,
          time: this.editedItem.time,
          activation: this.editedItem.activation
        });
      } else {
        this.validate();
      }
      this.close();
      this.resetValidation();
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
