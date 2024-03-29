<script>
import { required, email, max } from "vee-validate/dist/rules";
import {
  extend,
  ValidationObserver,
  ValidationProvider,
  setInteractionMode
} from "vee-validate";

setInteractionMode("eager");

extend("required", {
  ...required,
  message: "{_field_} can not be empty"
});

extend("max", {
  ...max,
  message: "{_field_} may not be greater than {length} characters"
});

extend("email", {
  ...email,
  message: "Email must be valid"
});

export default {
  name: "AuthModal",
  components: {
    ValidationProvider,
    ValidationObserver
  },
  data: () => ({
    selectedAuth: "login",
    dialog: false,
    name: "",
    email: "",
    password: ""
  }),
  methods: {
    async loginReq() {
      await this.$store.dispatch('makeLoginRequest', {
        email: this.email,
        password: this.password
      });
      this.dialog = false;
    },
    async registerReq() {
      await this.$store.dispatch('makeRegisterRequest', {
        email: this.email,
        password: this.password,
        name: this.name
      });
      this.dialog = false;
    },
    logout() {
      this.$store.dispatch('logout');
    }
  },
  computed: {
    token() {
      return this.$store.getters.getToken;
    }
  }
};
</script>

<template>
  <div>
    <v-dialog v-model="dialog" max-width="500" v-if="!token">
      <template v-slot:activator="{ on, attrs }">
        <v-btn color="primary" dark v-bind="attrs" v-on="on">
          Authorization
        </v-btn>
      </template>
      <v-card>
        <v-card-title class="headline">Authorization</v-card-title>
        <v-tabs>
          <v-tab @change="selectedAuth = 'login'">Login</v-tab>
          <v-tab @change="selectedAuth = 'register'">Register</v-tab>
        </v-tabs>
        <v-card-text>
          <ValidationObserver ref="observer" v-if="selectedAuth === 'login'">
            <form>
              <ValidationProvider
                v-slot="{ errors }"
                name="email"
                rules="required|email"
              >
                <v-text-field
                  v-model="email"
                  :error-messages="errors"
                  label="E-mail"
                  required
                ></v-text-field>
              </ValidationProvider>
              <ValidationProvider
                v-slot="{ errors }"
                name="password"
                type="password"
                rules="required"
              >
                <v-text-field
                  v-model="password"
                  :error-messages="errors"
                  label="Password"
                  required
                ></v-text-field>
              </ValidationProvider>
            </form>
          </ValidationObserver>

          <ValidationObserver ref="observer" v-if="selectedAuth === 'register'">
            <form>
              <ValidationProvider
                v-slot="{ errors }"
                name="name"
                rules="required|max:25"
              >
                <v-text-field
                  v-model="name"
                  :counter="25"
                  :error-messages="errors"
                  label="Name"
                  required
                ></v-text-field>
              </ValidationProvider>
              <ValidationProvider
                v-slot="{ errors }"
                name="email"
                rules="required|email"
              >
                <v-text-field
                  v-model="email"
                  :error-messages="errors"
                  label="E-mail"
                  required
                ></v-text-field>
              </ValidationProvider>
              <ValidationProvider
                v-slot="{ errors }"
                name="password"
                type="password"
                rules="required"
              >
                <v-text-field
                  v-model="password"
                  :error-messages="errors"
                  label="Password"
                  required
                ></v-text-field>
              </ValidationProvider>
            </form>
          </ValidationObserver>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="loginReq"
            v-if="selectedAuth === 'login'"
          >
            Log in
          </v-btn>
          <v-btn
            color="blue darken-1"
            text
            @click="registerReq"
            v-if="selectedAuth === 'register'"
          >
            Register
          </v-btn>
          <v-btn color="blue darken-1" text @click="dialog = false">
            Close
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <div v-if="token">
      <v-btn color="primary" dark @click="logout">
        Exit
      </v-btn>
    </div>
  </div>
</template>
