<template>
  <v-app>
    <v-content>
      <v-container>
        <v-card>
          <v-form ref="form" v-model="valid" class="padding">
            <v-img max-width="500" src="./assets/EloGroup.png"></v-img>
            <v-container maxWidth class="container">
              <v-text-field v-model="name" :rules="rulesName" label="Nome Completo"></v-text-field>
              <v-text-field
                placeholder="(00) 00000-0000"
                v-mask="'(##) #####-####'"
                min="15"
                :rules="rulesPhone"
                v-model="phone"
                label="Telefone"
              ></v-text-field>
              <v-radio-group row label="Possui redes sociais ?" v-model="hasSocial">
                <v-radio color="orange" label="Sim" value="yes"></v-radio>
                <v-radio color="orange" label="Não" value="no"></v-radio>
              </v-radio-group>

              <div v-if="hasSocial === 'yes'">
                <v-checkbox
                  color="orange"
                  label="Facebook"
                  v-model="socialNetworks"
                  value="facebook"
                  :error="!hasChosenSocial"
                ></v-checkbox>
                <v-checkbox
                  color="orange"
                  label="Instagram"
                  v-model="socialNetworks"
                  value="instagram"
                  :error="!hasChosenSocial"
                ></v-checkbox>
                <v-checkbox
                  color="orange"
                  label="LinkedIn"
                  v-model="socialNetworks"
                  value="linkedin"
                  :error="!hasChosenSocial"
                ></v-checkbox>

                <p v-if="!hasChosenSocial" class="red--text">Escolha uma rede social</p>
              </div>
              <v-select
                :rules="rulesHow"
                v-model="metOption"
                label="Como nos conheceu ?"
                :items="items"
              ></v-select>
            </v-container>
            <v-btn :disabled="disableSubmit" color="secondary" block @click="submitForm">ENVIAR</v-btn>
          </v-form>
        </v-card>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import api from "./axios/axios";
export default {
  name: "App",
  data: () => ({
    valid: true,
    disableSubmit: false,
    items: ["TV", "Internet", "Outro"],
    phone: "",
    name: "",
    phoneMask: "(##) #####-####",
    metOption: "",
    socialNetworks: [],
    hasSocial: "no",
    rulesName: [
      input => {
        let nameReg = new RegExp(/(\w.+\s).+/i);
        return nameReg.test(input) || "Por favor, insira seu nome completo";
      }
    ],
    rulesPhone: [
      input => {
        return input.length === 15
          ? true
          : "Preencha seu número de telefone no formato (##) #####-####";
      }
    ],
    rulesHow: [
      input => {
        return input != ""
          ? true
          : "Escolha um dos meios pelo qual nos conheceu";
      }
    ]
  }),
  computed: {
    hasChosenSocial() {
      return this.socialNetworks.length > 0;
    }
  },
  methods: {
    validateForm() {
      this.$refs.form.validate();
    },
    submitForm() {
      this.validateForm();
      this.valid = this.hasSocial === "yes" ? this.hasChosenSocial : this.valid;
      if (!this.valid) return;

      let data = {
        name: this.name,
        phone: this.phone,
        metOption: this.metOption
      };

      if (this.hasSocial && this.socialNetworks.length > 0) {
        data.socialNetworks = this.socialNetworks;
      }

      api.post("/", data);

      this.disableSubmit = true;
    }
  }
};
</script>

<style>
.padding {
  padding: 2rem;
}
</style>
