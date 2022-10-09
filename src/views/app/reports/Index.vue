<template>
  <v-container fluid grid-list-xl>
    <v-btn bottom color="green darken-4" dark fab fixed right @click="dialog = !dialog">
      <v-icon>mdi-plus</v-icon>
    </v-btn>
    <v-dialog v-model="dialog" width="900px">
      <v-card elevation="15" class="special-scroll">
        <v-card-title>Enrégistrer la paie du moi</v-card-title>
        <v-stepper v-model="e6" vertical>
          <v-stepper-step :complete="e6 > 1" step="1">Enrégistrement <small>Gérer la paie des utilisateurs pendant le moi</small></v-stepper-step>
          <v-stepper-content step="1">
            <v-row class="mx-2">
              <v-col class="align-center justify-space-between" cols="12">
                <v-row align="center" class="mr-0">
                <v-select v-model="editedPaie.full_name" :items="personelFullName" clearable prepend-inner-icon="mdi-camera-control" label="Chosir l'employé" />
                </v-row>
              </v-col>
              <v-col cols="12">
                <v-menu v-model="menu2" :close-on-content-click="false" :nudge-right="40" transition="scale-transition" offset-y min-width="290px">
                  <template v-slot:activator="{ on }">
                    <v-text-field v-model="editedPaie.dateDebut" prepend-inner-icon="mdi-calendar" label="Date en cours ..." readonly v-on="on"/>
                  </template>
                  <v-date-picker v-model="editedPaie.dateDebut" @input="menu2 = false"></v-date-picker>
                </v-menu>
              </v-col>
            </v-row>
            <v-row class="mx-2" v-show="editedPaie.full_name && editedPaie.dateDebut">
              <v-col cols="4" v-if="editedPaie.full_name">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.base_salary" clearable prepend-inner-icon="mdi-buffer" label="Salaire de base" disabled />
                </v-row>
              </v-col>
              <v-col cols="4">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.prime" clearable prepend-inner-icon="mdi-buffer" label="Prime" />
                </v-row>
              </v-col>
              <v-col cols="4">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.n_t" clearable prepend-inner-icon="mdi-buffer" label="N/T" />
                </v-row>
              </v-col>
              <v-col cols="3">
                <v-text-field v-model="editedPaie.accompli" type="number" min="0" clearable prepend-inner-icon="mdi-orbit" label="Accompli"/>
              </v-col>
              <v-col cols="6">
                <v-text-field v-model="editedPaie.salaire_mensuel" type="number" min="0" clearable prepend-inner-icon="mdi-orbit" label="Salaire mensuel"/>
              </v-col>
              <v-col cols="3">
                <v-text-field v-model="editedPaie.rav" clearable prepend-inner-icon="mdi-text" label="RAV" />
              </v-col>
              <v-col cols="4">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.economie" clearable prepend-inner-icon="mdi-buffer" label="Economie" />
                </v-row>
              </v-col>
              <v-col cols="4">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.infirmerie" clearable prepend-inner-icon="mdi-buffer" label="Infirmerie" />
                </v-row>
              </v-col>
              <v-col cols="4">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.gant" clearable prepend-inner-icon="mdi-buffer" label="Gant" />
                </v-row>
              </v-col>
              <v-col cols="3">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.examen" clearable prepend-inner-icon="mdi-buffer" label="Examen" />
                </v-row>
              </v-col>
              <v-col cols="3">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.d_a" clearable prepend-inner-icon="mdi-buffer" label="DA" />
                </v-row>
              </v-col>
              <v-col cols="3">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.nap" clearable prepend-inner-icon="mdi-buffer" label="NAP" />
                </v-row>
              </v-col>
              <v-col cols="3">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedPaie.illisible" clearable prepend-inner-icon="mdi-buffer" label="Illisible" />
                </v-row>
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <v-btn color="green darken-3" dark small fab class="ml-3 white--text" @click="save">
                  <v-icon small>
                    mdi-telegram
                  </v-icon>
                </v-btn>
              </v-col>
            </v-row>
          </v-stepper-content>
        </v-stepper>
      </v-card>
    </v-dialog>
    <v-layout wrap class="px-0 py-12">
      <v-flex sm4 xs4 md4 lg4>
        <v-card class="mx-auto" tile>
          <v-list-item three-line>
            <v-list-item-content>
              <div class="overline mb-4">Nombre d'employés</div>
              <v-list-item-title class="indigo--text display-1" v-if="personnel">{{ personnel.length }} <small class="caption font-weight-light">Employés</small></v-list-item-title>
              <v-list-item-subtitle class="ml-2 caption grey--text font-weight-light">Enrégistrés pour l'instant</v-list-item-subtitle>
            </v-list-item-content>

            <v-list-item-avatar size="80" tile color="indigo" style="border-radius: 5px" class="elevation-5">
              <v-icon dark size="40">mdi-currency-sign</v-icon>
            </v-list-item-avatar>
          </v-list-item>
        </v-card>
      </v-flex>
      <v-flex sm4 xs4 md4 lg4>
        <v-card class="mx-auto" tile>
          <v-list-item three-line>
            <v-list-item-content>
              <div class="overline mb-4">Employé le plus chers</div>
              <v-list-item-title class="red--text display-1">{{ plusChers + ' F. CFA' }}</v-list-item-title>
              <v-list-item-subtitle class="ml-2 caption grey--text font-weight-light">salaire de base uniquement</v-list-item-subtitle>
            </v-list-item-content>

            <v-list-item-avatar size="80" tile color="red" style="border-radius: 5px" class="elevation-5">
              <v-icon dark size="40">mdi-currency-sign</v-icon>
            </v-list-item-avatar>
          </v-list-item>
        </v-card>
      </v-flex>
      <v-flex sm4 xs4 md4 lg4>
        <v-card class="mx-auto" tile>
          <v-list-item three-line>
            <v-list-item-content>
              <div class="overline mb-4">Employé le moins chers</div>
              <v-list-item-title class="warning--text display-1">{{ moinsChers + ' F. CFA' }}</v-list-item-title>
              <v-list-item-subtitle class="ml-2 caption grey--text font-weight-light">Salaire de base uniquement</v-list-item-subtitle>
            </v-list-item-content>

            <v-list-item-avatar size="80" tile color="warning" style="border-radius: 5px" class="elevation-5">
              <v-icon dark size="40">mdi-currency-sign</v-icon>
            </v-list-item-avatar>
          </v-list-item>
        </v-card>
      </v-flex>
      <v-flex sm12 xs12 md12 lg12>
        <div class="year-navigation">
          <v-btn @click="changeMonth(0)" id="forTooltip" class="hover-rotate ml-1" width="30" height="30" color="#1b5e20" dark fab small>
            <v-icon small dark>mdi-chevron-left</v-icon>
          </v-btn>
          <span class="year" v-show="currentYear"> {{ currentMonth + ' ' + currentYear }} </span>
          <v-btn @click="changeMonth(1)" class="hover-rotate ml-1" width="30" height="30" color="#1b5e20" dark fab small>
            <v-icon small dark>mdi-chevron-right</v-icon>
          </v-btn>
        </div>
        <v-card tile class="mx-auto mt-5">
          <v-sheet class="v-sheet--offset mx-auto py-3 px-3" color="green darken-4" elevation="10" max-width="calc(100% - 32px)">
            <v-card-text class="pt-0">
              <div class="title font-weight-thin mb-0 white--text">Gestion de la paie du personnel par moi</div>
            </v-card-text>
          </v-sheet>

          <v-card-text class="pt-0" v-if="paie">
            <v-data-table :headers="headers" :items="paieToShow" :items-per-page="4" class="mb-3">
              <template v-slot:item.statut="{ item }">
                <v-btn dark fab :color="color[item.statut]" width="10" height="10"></v-btn>
              </template>
              <template v-slot:item.action="{ item }">
                <v-btn dark fab class="elevation-3" max-width="35" max-height="35" color="green">
                  <v-icon small @click="editSouscription(item)">mdi-pencil-outline</v-icon>
                </v-btn>
                <v-btn dark fab class="mx-2 elevation-3" max-width="35" max-height="35" color="red">
                  <v-icon small @click="deleteSouscription(item)">mdi-delete-outline</v-icon>
                </v-btn>
              </template>
              <template v-slot:no-data>
                <p class="dark mt-5">Aucun enrégistrement effectué pour le moment</p>
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
      currentMonthId: null,
      currentMonth: null,
      currentYear: null,
      modalHide: true,
      oldId: null,
      addStep: 0,
      countUpdate: 0,
      totalNbrDay: 0,
      personelFullName: [],
      personelBaseSalary: [],
      menu2: false,
      editedPaie: {
        id: null,
        full_name: null,
        dateDebut: null,
        base_salary: 0,
        prime: 0,
        n_t: 0,
        accompli: 0,
        salaire_mensuel: 0,
        rav: 0,
        economie: 0,
        infirmerie: 0,
        gant: 0,
        examen: 0,
        d_a: 0,
        nap: 0,
        illisible: 0
      },
      newPaie: {
        id: null,
        full_name: null,
        dateDebut: null,
        base_salary: 0,
        prime: 0,
        n_t: 0,
        accompli: 0,
        salaire_mensuel: 0,
        rav: 0,
        economie: 0,
        infirmerie: 0,
        gant: 0,
        examen: 0,
        d_a: 0,
        nap: 0,
        illisible: 0
      },
      alert: true,
      loading: false,
      selection: 1,
      personnel: [],
      paie: [],
      paieToShow: [],
      plusChers: 0,
      moinsChers: 0,
      depenses: 0,
      depensesRemise: 0,
      economie: null,
      headers: [
        { text: 'Nom complet', value: 'full_name' },
        { text: 'Salaire de Base', value: 'base_salary' },
        { text: 'Primes', value: 'prime' },
        { text: 'N/T', value: 'n_t' },
        { text: 'Accompli', value: 'accompli' },
        { text: 'Salaire mensuel', value: 'salaire_mensuel' },
        { text: 'RAV', value: 'rav' },
        { text: 'Economisé', value: 'economie' },
        { text: 'Infirmerie', value: 'infirmerie' },
        { text: 'Gant', value: 'gant' },
        { text: 'Examen', value: 'examen' },
        { text: 'DA', value: 'd_a' },
        { text: 'NAP', value: 'nap' },
        { text: 'Illisible', value: 'illisible' },
        { text: '', value: 'action', sortable: false }
      ],
      month: ['Janvier', 'Février', 'Mars', 'Avril', 'Mai', 'Juin', 'Juillet', 'Août', 'Septembre', 'Octobre', 'Novembre', 'Décemblre']
    }
  },
  watch: {
    dialog (val) {
      val || this.close()
    },
  },
  methods: {
    changeMonth (id) {
      this.paieToShow = []
      if (id === 0) { this.currentMonthId--, this.currentMonth = this.month[this.currentMonthId]} else { this.currentMonthId++, this.currentMonth = this.month[this.currentMonthId]}
      this.paie.forEach(elt => {
        if(new Date(elt.dateDebut).getMonth() === this.currentMonthId) {
          this.paieToShow.push(elt)
        }
      })
    },
    saveToLocal () {
      let data = JSON.stringify(this.paie)
      window.localStorage.setItem('paie_' + this.currentUser.username, data)
    },
    editSouscription (item) {
      this.editedIndex = this.paie.indexOf(item)
      this.editedPaie = Object.assign({}, item)
      this.dialog = true
    },
    deleteSouscription (item) {
      const index = this.paie.indexOf(item)
      if (confirm('Êtes vous sûr de vouloir supprimer cet enrégistrement ?')) {
        this.paie.splice(index, 1)
        this.saveToLocal()
      }
    },
    close () {
      this.dialog = false
      setTimeout(() => {
        this.editedPaie = Object.assign({}, this.newPaie)
        this.editedIndex = -1
        this.e6 = 1
      }, 300)
    },
    save () {
      if (this.editedIndex > -1) {
        Object.assign(this.paie[this.editedIndex], this.editedPaie)
      } else {
        this.paie.push({
          id: Math.floor(new Date () * Math.random() * Math.floor(9)),
          full_name: this.editedPaie.full_name,
          dateDebut: this.editedPaie.dateDebut,
          base_salary: this.editedPaie.base_salary,
          prime: this.editedPaie.prime,
          n_t: this.editedPaie.n_t,
          accompli: this.editedPaie.accompli,
          salaire_mensuel: this.editedPaie.salaire_mensuel,
          rav: this.editedPaie.rav,
          economie: this.editedPaie.economie,
          infirmerie: this.editedPaie.infirmerie,
          gant: this.editedPaie.gant,
          examen: this.editedPaie.examen,
          d_a: this.editedPaie.d_a,
          nap: this.editedPaie.nap,
          illisible: this.editedPaie.illisible
        })
      }
      this.saveToLocal()
      this.close()
    }
  },
  mounted () {
    this.currentYear = this.currentDate.getFullYear()
    this.currentMonthId = parseInt(this.currentDate.getMonth())
    this.currentMonth = this.month[this.currentMonthId]
    this.currentUser = JSON.parse(window.localStorage.getItem('tmp-user-connected'))
    if (window.localStorage.getItem('paie_' + this.currentUser.username) !== null && window.localStorage.getItem('paie_' + this.currentUser.username).length > 0) {
      this.paie = JSON.parse(window.localStorage.getItem('paie_' + this.currentUser.username))
      this.paie.forEach(paie => {
        if (new Date(paie.dateDebut).getMonth() === this.currentDate.getMonth()) {
          this.paieToShow.push(paie)
        }
      })
    }
    if (window.localStorage.getItem('personnel_' + this.currentUser.username) !== null && window.localStorage.getItem('personnel_' + this.currentUser.username).length > 0) {
      this.personnel = JSON.parse(window.localStorage.getItem('personnel_' + this.currentUser.username))
      this.moinsChers = this.personnel[0].base_salary

      this.personnel.forEach(personnel => {
        if (personnel.base_salary > this.plusChers) { this.plusChers = parseInt(personnel.base_salary) }
        if (personnel.base_salary < this.moinsChers) { this.moinsChers = parseInt(personnel.base_salary) }
        let tmpPersonnelFullName = { text: personnel.full_name, value: personnel.full_name }
        let tmpPersonnelBaseSalary = { text: personnel.base_salary, value: personnel.base_salary }
        this.personelFullName.push(tmpPersonnelFullName)
        this.personelBaseSalary.push(tmpPersonnelBaseSalary)
      })
    }
  },
  updated () {
    if (this.editedPaie.full_name) {
      let index = this.personnel.map(e => e.full_name).indexOf(this.editedPaie.full_name)
      this.editedPaie.base_salary = this.personnel[index].base_salary
    }
  }
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
  .year-navigation {
      margin-left: 20px;
      height: 40px;
      position: relative;
      display: flex;
      align-items: center;
      margin-bottom: 35px;
      .prev-year,
      .next-year {
          display: inline-block;
          width: 30px;
          height: 30px;
          text-align: center;
          font-size: 20px;
          line-height: 30px;
          border: solid 1px #1b5e20;
          border-radius: 50%;
          color: #1b5e20;
          margin: 5px;
          cursor: pointer;
      }
      .year {
          color: #1b5e20;
          margin: 5px 15px;
          font-size: 18px;
      }
  }
</style>
