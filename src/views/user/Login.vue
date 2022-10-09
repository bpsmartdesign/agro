<template>
  <v-app id="inspire">
    <v-content>
      <v-container class="fill-height" fluid>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="6">
            <div class="display-1 font-italic font-weight-light green--text text-transform-uppercase mb-3">Welcome <span class="subtitle-1">to that awesome plateforme</span></div>
            <v-alert transition="scale-transition" dismissible v-show="errorConnection" v-model="alert" dense outlined type="error">{{ errorConnection }}</v-alert>
            <v-tabs dark background-color="green darken-4">
              <v-tab>Sign In</v-tab>

              <v-tab-item>
                <v-card class="elevation-12" flat tile>
                  <v-card-text>
                    <v-form>
                      <v-text-field color="green" label="Login" v-model="userConnect.username" name="login" prepend-icon="mdi-account-arrow-right-outline" type="text"/>
                      <v-text-field color="green" id="password" v-model="userConnect.password" label="Password" name="password" prepend-icon="mdi-lock-outline" type="password"/>
                    </v-form>
                  </v-card-text>
                  <v-card-actions>
                    <v-dialog v-model="forgetPass" width="500px">
                      <v-card elevation="15" tile flat min-height="200px">
                        <v-container>
                          <v-card-text>
                            <div>Mot de Passe oublié</div>
                            <v-row>
                              <v-col cols='10'>
                                <v-text-field color="green" class="mb-0 mt-5" outlined label="Prepend inner" prepend-inner-icon="mdi-alert-decagram-outline"></v-text-field>
                              </v-col>
                              <v-col cols='2' class="mb-0 mt-7">
                                <v-btn fab dark small color="green darken-4">
                                  <v-icon dark>mdi-telegram</v-icon>
                                </v-btn>
                              </v-col>
                            </v-row>
                            <span class="text--primary caption mt-0">Entrer votre adresse mail pour récupérer votre mot de passe</span>
                          </v-card-text>
                        </v-container>
                      </v-card>
                    </v-dialog>
                    <span class="hoverPointer ml-4 mt-0 caption font-weight-light" style="color: #1b5e20" @click="forgetPass = true">Mot de passe oublié ?</span>
                    <v-spacer />
                    <v-btn color="green darken-4" dark tile class="elevation-5" @click="login">Sign</v-btn>
                  </v-card-actions>
                </v-card>
              </v-tab-item>
            </v-tabs>
          </v-col>
        </v-row>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
export default {
  data: () => ({
    tab: null,
    alert: false,
    forgetPass: false,
    activateAccount: false,
    errorConnection: false,
    confirmationCode: "",
    users: [],
    currentUser: null,
    userConnect: {
      username: null,
      password: null
    }
  }),
  methods: {
    setRandomId() {
      let result = "";
      let char = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";

      for (let i = 0; i < 15; i++) {
        result += char.charAt(Math.floor(Math.random() * char.length));
      }

      return result;
    },

    login() {
      this.currentUser = {
        id: this.setRandomId(),
        username: this.userConnect.username 
      }
      localStorage.setItem('tmp-user-connected', JSON.stringify(this.currentUser))
      this.$router.push({ path: '/' })
    }
  }
};
</script>

<style lang="scss" scoped>
.hoverPointer {
  cursor: pointer;
}
</style>