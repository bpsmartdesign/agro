<template>
  <v-container fluid grid-list-xl>
    <v-btn bottom color="green darken-4" dark fab fixed right @click="dialog = !dialog">
      <v-icon>mdi-plus</v-icon>
    </v-btn>
    <v-dialog v-model="dialog" width="900px">
      <v-card elevation="15" class="special-scroll">
        <v-card-title>Enrégistrer un nouvel employé</v-card-title>
        <v-stepper v-model="e6" vertical>
          <v-stepper-step :complete="e6 > 1" step="1">Enrégistrement <small>Renseigner les détails sur le personnel</small></v-stepper-step>
          <v-stepper-content step="1">
            <v-row class="mx-2">
              <v-col class="align-center justify-space-between" cols="12">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPersonnel.full_name" clearable prepend-inner-icon="mdi-account-question-outline" label="Nom complet de l'employé" />
                </v-row>
              </v-col>
              <v-col cols="12">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPersonnel.base_salary" clearable prepend-inner-icon="mdi-buffer" label="Salaire de base" />
                </v-row>
              </v-col>
              <v-col cols="12">
                <v-text-field v-model="editedPersonnel.fonction" clearable prepend-inner-icon="mdi-orbit" label="Fonction"/>
              </v-col>
              <v-col cols="4">
                <v-text-field v-model="editedPersonnel.contact" clearable prepend-inner-icon="mdi-text" label="Contact" />
              </v-col>
              <v-col cols="4">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPersonnel.adresse" clearable prepend-inner-icon="mdi-buffer" label="Adresse" />
                </v-row>
              </v-col>
              <v-col cols="4">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPersonnel.email" clearable prepend-inner-icon="mdi-buffer" label="E-mail" />
                </v-row>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <v-btn v-show="editedPersonnel.full_name && editedPersonnel.fonction" color="green darken-3" dark small fab class="ml-3 white--text" @click="save">
                  <v-icon small>mdi-telegram</v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </v-stepper-content>
        </v-stepper>
      </v-card>
    </v-dialog>
    <v-layout wrap class="px-0 py-12">
      <v-flex sm12 xs12 md12 lg12>
        <v-card tile class="mx-auto mt-8">
          <v-sheet class="v-sheet--offset mx-auto py-3 px-3" color="green darken-4" elevation="10" max-width="calc(100% - 32px)">
            <v-card-text class="pt-0">
              <div class="title font-weight-thin mb-0 white--text">Liste du personnel</div>
            </v-card-text>
          </v-sheet>

          <v-card-text class="pt-0" v-if="personnel">
            <v-data-table :headers="headers" :items="personnel" :items-per-page="4" class="mb-3">
              <template v-slot:item.statut="{ item }">
                <v-btn dark fab :color="color[item.statut]" width="10" height="10"></v-btn>
              </template>
              <template v-slot:item.action="{ item }">
                <v-btn dark fab class="elevation-3" max-width="35" max-height="35" color="green">
                  <v-icon small @click="editPersonnel(item)">mdi-pencil-outline</v-icon>
                </v-btn>
                <v-btn dark fab class="mx-2 elevation-3" max-width="35" max-height="35" color="red">
                  <v-icon small @click="deletePersonnel(item)">mdi-delete-outline</v-icon>
                </v-btn>
              </template>
              <template v-slot:no-data>
                <p class="dark mt-5">Aucun personnel enrégistré pour le moment</p>
              </template>
            </v-data-table>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>

export default {
  data () {
    return {
      currentUser: null,
      editedIndex: -1,
      dialog: false,
      e6: 1,
      currentDate: new Date(),
      editedPersonnel: {
        id: null,
        full_name: null,
        base_salary: 0,
        contact: null,
        adresse: null,
        fonction: null,
        email: null
      },
      newPersonnel: {
        id: null,
        full_name: null,
        base_salary: 0,
        contact: null,
        adresse: null,
        fonction: null,
        email: null
      },
      personnel: [],
      headers: [
        { text: 'Nom complet', value: 'full_name' },
        { text: 'Fonction', value: 'fonction' },
        { text: 'Contact', value: 'contact' },
        { text: 'Adresse', value: 'adresse' },
        { text: 'Email', value: 'email' },
        { text: 'Salaire de Base', value: 'base_salary' },
        { text: '', value: 'action', sortable: false }
      ]
    }
  },
  watch: {
    dialog (val) {
      val || this.close()
    },
  },
  methods: {
    saveToLocal () {
      let data = JSON.stringify(this.personnel)
      window.localStorage.setItem('personnel_' + this.currentUser.username, data)
    },
    editPersonnel (item) {
      this.editedIndex = this.personnel.indexOf(item)
      this.editedPersonnel = Object.assign({}, item)
      this.dialog = true
    },
    deletePersonnel (item) {
      const index = this.personnel.indexOf(item)
      if (confirm('Êtes vous sûr de vouloir supprimer cet employé ?')) {
        this.personnel.splice(index, 1)
        this.saveToLocal()
      }
    },
    close () {
      this.dialog = false
      setTimeout(() => {
        this.editedPersonnel = Object.assign({}, this.newPersonnel)
        this.editedIndex = -1
        this.e6 = 1
      }, 300)
    },
    save () {
      if (this.editedIndex > -1) {
        Object.assign(this.personnel[this.editedIndex], this.editedPersonnel)
      } else {
        this.personnel.push({
          id: Math.floor(new Date () * Math.random() * Math.floor(9)),
          full_name: this.editedPersonnel.full_name,
          base_salary: this.editedPersonnel.base_salary,
          contact: this.editedPersonnel.contact,
          adresse: this.editedPersonnel.adresse,
          fonction: this.editedPersonnel.fonction,
          email: this.editedPersonnel.email
          })
      }
      this.saveToLocal()
      this.close()
    }
  },
  mounted () {
    this.currentUser = JSON.parse(window.localStorage.getItem('tmp-user-connected'))
    if (window.localStorage.getItem('personnel_' + this.currentUser.username) !== null && window.localStorage.getItem('personnel_' + this.currentUser.username).length > 0) {
      this.personnel = JSON.parse(window.localStorage.getItem('personnel_' + this.currentUser.username))
    }
  },
}
</script>

<style lang="scss" scoped>
  .v-sheet--offset {
    top: -24px;
    position: relative;
  }
  .card-prepend {
    width: 90%;
    margin-left: 5%;

    .card-offset {
      height: 200px;
    }

    .card-content {
      position: absolute;
      top: 0;
    }
  }
</style>
