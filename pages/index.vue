<template>
  <v-app>
    <div class="text-center">
      <!-- <v-snackbar v-model="dialog" color="primary">
        {{ message }}
      </v-snackbar> -->
      <v-dialog v-model="dialog" width="300">
        <v-card style="background: none">
          <v-toolbar style="background: none" flat dense>
            <v-spacer></v-spacer>
            <!-- <v-icon @click="dialog = false">mdi-close</v-icon> -->
          </v-toolbar>

          <v-card-text>
            <p class="text-center">
              <v-img
                :src="response_image"
                alt="Avatar"
                height="125px"
                width="125px"
                style="display: inline-block"
              ></v-img>
            </p>
            <!-- <p class="text-center">
              {{ message }}
            </p> -->
          </v-card-text>
        </v-card>
      </v-dialog>
    </div>
    <v-app-bar color="primary" fixed app>
      <v-row>
        <v-col cols="4">
          <v-img
            src="/logo-2.png"
            max-height="100"
            max-width="150"
            contain
            class="pa-2"
          ></v-img>
        </v-col>

        <v-col cols="4">
          <v-toolbar-title class="text-center white--text pa-2">
            <b>Tanent Registration</b>
          </v-toolbar-title>
        </v-col>
      </v-row>
    </v-app-bar>

    <v-container class="mt-15">
      <v-row>
        <v-col cols="12">
          <div class="text-center">
            <Camera
              ref="CameraComponent"
              @imageSrc="
                (e) => {
                  payload.profile_picture = e;
                }
              "
            />
            <span v-if="errors && errors.logo" class="error--text mt-2">{{
              errors.logo[0]
            }}</span>
          </div>
        </v-col>

        <v-col cols="6">
          <v-autocomplete
            @change="getRelatedChildDetails"
            label="Room Category"
            outlined
            v-model="payload.room_category_id"
            :items="room_categories"
            dense
            item-text="name"
            item-value="id"
            :hide-details="!errors.room_category_id"
            :error-messages="
              errors && errors.room_category_id
                ? errors.room_category_id[0]
                : ''
            "
          >
          </v-autocomplete>
        </v-col>
        <v-col cols="6">
          <v-autocomplete
            label="Room Sub Category"
            outlined
            v-model="payload.room_sub_category_id"
            :items="filtered_room_sub_categories"
            dense
            item-text="name"
            item-value="id"
            :hide-details="!errors.room_sub_category_id"
            :error-messages="
              errors && errors.room_sub_category_id
                ? errors.room_sub_category_id[0]
                : ''
            "
          >
          </v-autocomplete>
        </v-col>
        <v-col cols="6">
          <v-autocomplete
            @change="getRoomsByFloorId"
            label="Floor Number"
            outlined
            v-model="payload.floor_id"
            :items="floors"
            dense
            item-text="floor_number"
            item-value="id"
            :hide-details="!errors.floor_id"
            :error-messages="
              errors && errors.floor_id ? errors.floor_id[0] : ''
            "
          >
          </v-autocomplete>
        </v-col>
        <v-col cols="6">
          <v-autocomplete
            @change="getRoomNumber(payload.room_id)"
            label="Room"
            outlined
            v-model="payload.room_id"
            :items="rooms"
            dense
            item-text="room_number"
            item-value="id"
            :hide-details="!errors.room_id"
            :error-messages="errors && errors.room_id ? errors.room_id[0] : ''"
          >
          </v-autocomplete>
        </v-col>
        <v-col cols="6">
          <v-text-field
            label="Age"
            v-model="payload.age"
            dense
            class="text-center"
            outlined
            :hide-details="!errors.age"
            :error-messages="errors && errors.age ? errors.age[0] : ''"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-autocomplete
            label="Member Type"
            outlined
            v-model="payload.member_type"
            :items="member_types"
            dense
            :hide-details="!errors.member_type"
            :error-messages="
              errors && errors.member_type ? errors.member_type[0] : ''
            "
          >
          </v-autocomplete>
        </v-col>
        <v-col cols="6">
          <v-text-field
            v-model="payload.first_name"
            dense
            outlined
            :hide-details="!errors.first_name"
            :error-messages="
              errors && errors.first_name ? errors.first_name[0] : ''
            "
            label="First Name *"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            v-model="payload.last_name"
            dense
            outlined
            :hide-details="!errors.last_name"
            :error-messages="
              errors && errors.last_name ? errors.last_name[0] : ''
            "
            label="Last Name *"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-autocomplete
            label="Term"
            outlined
            v-model="payload.term"
            :items="[`Long Term`, `Short Term`]"
            dense
            :hide-details="!errors.term"
            :error-messages="errors && errors.term ? errors.term[0] : ''"
          >
          </v-autocomplete>
        </v-col>
        <v-col cols="6">
          <v-radio-group
            class="ma-0 px-2 pa-0"
            v-model="payload.gender"
            row
            :hide-details="!errors.gender"
            :error-messages="errors && errors.gender ? errors.gender[0] : ''"
          >
            <v-radio label="Male" value="Male"></v-radio>
            <v-radio label="Female" value="Female"></v-radio>
            <v-radio label="Other" value="Other"></v-radio>
          </v-radio-group>
        </v-col>
        <v-col cols="6">
          <v-text-field
            label="Email"
            v-model="payload.email"
            dense
            class="text-center"
            outlined
            :hide-details="!errors.email"
            :error-messages="errors && errors.email ? errors.email[0] : ''"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-menu
            v-model="menu3"
            :close-on-content-click="false"
            :nudge-right="40"
            transition="scale-transition"
            offset-y
            min-width="auto"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-text-field
                v-model="payload.date_of_birth"
                label="Date of Birth"
                append-icon="mdi-calendar"
                outlined
                dense
                readonly
                v-bind="attrs"
                v-on="on"
                :hide-details="!errors.date_of_birth"
                :error-messages="
                  errors && errors.date_of_birth ? errors.date_of_birth[0] : ''
                "
              ></v-text-field>
            </template>
            <v-date-picker
              v-model="payload.date_of_birth"
              @input="menu3 = false"
              no-title
              scrollable
            ></v-date-picker>
          </v-menu>
        </v-col>
        <v-col cols="6">
          <v-text-field
            label="Phone Number"
            v-model="payload.phone_number"
            dense
            class="text-center"
            outlined
            :hide-details="!errors.phone_number"
            :error-messages="
              errors && errors.phone_number ? errors.phone_number[0] : ''
            "
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            label="Whatsapp Number (optional)"
            v-model="payload.whatsapp_number"
            dense
            class="text-center"
            outlined
            :hide-details="!errors.whatsapp_number"
            :error-messages="
              errors && errors.whatsapp_number ? errors.whatsapp_number[0] : ''
            "
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            label="RFID"
            v-model="payload.rfid"
            dense
            class="text-center"
            outlined
            :hide-details="!errors.rfid"
            :error-messages="errors && errors.rfid ? errors.rfid[0] : ''"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            label="PIN"
            v-model="payload.pin"
            dense
            class="text-center"
            outlined
            :hide-details="!errors.pin"
            :error-messages="errors && errors.pin ? errors.pin[0] : ''"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            label="Nationality"
            v-model="payload.nationality"
            dense
            class="text-center"
            outlined
            :hide-details="!errors.nationality"
            :error-messages="
              errors && errors.nationality ? errors.nationality[0] : ''
            "
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-text-field
            label="Address"
            v-model="payload.address"
            dense
            class="text-center"
            outlined
            :hide-details="!errors.address"
            :error-messages="errors && errors.address ? errors.address[0] : ''"
          ></v-text-field>
        </v-col>
        <v-col cols="6">
          <v-menu
            v-model="menu"
            :close-on-content-click="false"
            :nudge-right="40"
            transition="scale-transition"
            offset-y
            min-width="auto"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-text-field
                v-model="payload.start_date"
                label="Start Date"
                append-icon="mdi-calendar"
                outlined
                dense
                readonly
                v-bind="attrs"
                v-on="on"
                :hide-details="!errors.start_date"
                :error-messages="
                  errors && errors.start_date ? errors.start_date[0] : ''
                "
              ></v-text-field>
            </template>
            <v-date-picker
              v-model="payload.start_date"
              @input="menu = false"
              no-title
              scrollable
            ></v-date-picker>
          </v-menu>
        </v-col>
        <v-col cols="6">
          <v-menu
            v-model="menu2"
            :close-on-content-click="false"
            :nudge-right="40"
            transition="scale-transition"
            offset-y
            min-width="auto"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-text-field
                v-model="payload.end_date"
                label="End Date"
                append-icon="mdi-calendar"
                outlined
                dense
                readonly
                v-bind="attrs"
                v-on="on"
                :hide-details="!errors.end_date"
                :error-messages="
                  errors && errors.end_date ? errors.end_date[0] : ''
                "
              ></v-text-field>
            </template>
            <v-date-picker
              v-model="payload.end_date"
              @input="menu2 = false"
              no-title
              scrollable
            ></v-date-picker>
          </v-menu>
        </v-col>
        <v-col cols="12">
          <v-row>
            <v-col cols="6">
              <v-btn
                rounded
                large
                block
                :loading="loading"
                color="grey"
                dark
                @click="reset"
              >
                Reset
              </v-btn>
            </v-col>
            <v-col cols="6">
              <v-btn
                rounded
                large
                block
                :loading="loading"
                color="primary"
                @click="submit"
              >
                Submit
              </v-btn>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
    </v-container>

    <!-- <Form /> -->

    <!-- <v-footer :absolute="true" style="background: none">
      <v-container>
        <v-row>
          <v-col cols="6">
            <v-btn
              rounded
              large
              block
              :loading="loading"
              color="grey"
              dark
              @click="reset"
            >
              Reset
            </v-btn>
          </v-col>
          <v-col cols="6">
            <v-btn
              rounded
              large
              block
              :loading="loading"
              color="primary"
              @click="submit"
            >
              Submit
            </v-btn>
          </v-col>
        </v-row></v-container
      >
    </v-footer> -->
  </v-app>
</template>

<script>
export default {
  data: () => ({
    tanent_number: "",
    tanentTypeDialog: false,
    response_image: "/success.png",
    message: "Record has been inserted",

    menu: false,
    menu2: false,
    menu3: false,

    time_in_menu: "",
    dialog: false,
    date_of_birth: false,
    floor_number: "",
    room_number: "",

    payload: {
      company_id: 2,
      member_type: "Primary",
      room_category_id: 1,
      room_sub_category_id: 2,
      floor_id: 1,
      room_id: 4,
      room_number: "101",
      age: "Incidunt officia fu",
      gender: "Male",
      first_name: "Rebekah",
      last_name: "Fleming",
      email: "coviha@mailinator.com",
      phone_number: "+1 (945) 447-5911",
      whatsapp_number: "31",
      rfid: "2222",
      pin: "Reiciendis do vel qu",
      nationality: "3333",
      address: "Modi voluptates odio",
      term: "Long Term",
      date_of_birth: "2024-02-16",
      start_date: "2024-02-16",
      end_date: "2024-02-16",
    },
    floors: [],
    rooms: [],

    response: null,
    loading: false,
    data: [],
    errors: [],
    purposes: [],
    room_categories: [],
    filtered_room_sub_categories: [],
    room_sub_categories: [],
    member_types: [],

    upload: {
      name: "",
    },
    fields: [],
  }),
  mounted() {},
  async created() {
    let config = {
      params: { company_id: this.payload.company_id },
    };
    let { data: room_categories } = await this.$axios.get(
      `/room-category-list`,
      config
    );
    this.room_categories = room_categories;

    let { data: room_sub_categories } = await this.$axios.get(
      `/room-sub-category-list`,
      config
    );

    let { data: member_types } = await this.$axios.get(`/get_member_types`);
    this.member_types = member_types;
    this.room_sub_categories = room_sub_categories;

    this.getRelatedChildDetails();
  },

  methods: {
    async getRelatedChildDetails() {
      await this.setRoomSubCategories(this.payload.room_category_id);
    },
    async setRoomSubCategories(id) {
      this.filtered_room_sub_categories = this.room_sub_categories.filter(
        (e) => e.room_category_id == id
      );

      await this.getFloorByCategory(id);
    },
    async getFloorByCategory(id) {
      let { data } = await this.$axios.get(`/room-floor-by-category/${id}`);
      this.floors = data;

      this.getRoomsByFloorId();
    },
    async getRoomsByFloorId() {
      let { data } = await this.$axios.get(`/room-by-floor-id`, {
        params: {
          company_id: this.payload.company_id,
          floor_id: this.payload.floor_id,
          room_category_id: this.payload.room_category_id,
        },
      });
      this.rooms = data;
    },

    getRoomNumber(room_id) {
      let { room_number } = this.rooms.find((e) => e.id == room_id);
      this.payload.room_number = room_number || 0;
    },
    reset() {
      this.$refs.CameraComponent.isClicked = false;
      this.errors = [];
      let company_id = this.payload.company_id;
      this.payload = {};
      this.payload.company_id = company_id;

      setTimeout(() => (this.dialog = false), 3000);
    },
    submit() {
      let formData = new FormData();

      for (let x in this.payload) {
        formData.append(x, this.payload[x]);
      }

      this.$axios
        .post(`tanent-register`, formData)
        .then(({ data }) => {
          // alert("Tanent has been registered");
          this.dialog = true;
          this.message = "Record has been inserted";
          this.reset();
        })
        .catch(({ response }) => {
          this.handleErrorResponse(response);
        });
    },
    handleErrorResponse(response) {
      if (!response) {
        alert("An unexpected error occurred");
        return;
      }
      let { status, data } = response;

      if (status && status == 422) {
        this.errors = data.errors;
        return;
      }

      alert("An unexpected error occurred");
    },
  },
};
</script>

<style>
.v-dialog.v-dialog--active {
  box-shadow: none !important;
}
</style>
