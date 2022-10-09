<template>
  <div>
    <v-btn bottom color="#ff4e00" dark fab fixed right @click="dialog = !dialog">
      <v-icon>mdi-plus</v-icon>
    </v-btn>
    <v-dialog v-model="dialog" width="800px">
      <v-card elevation="15" class="special-scroll">
        <v-card-title>Ajouter Souscription</v-card-title>
        <v-stepper v-model="e6" vertical>
          <v-stepper-step :complete="e6 > 1" step="1">Enrégistrement <small>Renseigner les détails sur la souscription</small></v-stepper-step>
          <v-stepper-content step="1">
            <v-row class="mx-2">
              <v-col class="align-center justify-space-between" cols="12">
                <v-row align="center" class="mr-0">
                  <v-text-field v-model="editedSouscription.souscription" clearable prepend-inner-icon="mdi-buffer" label="Nom de la souscription" />
                </v-row>
              </v-col>
              <v-col cols="6">
                <v-select v-model="editedSouscription.categorie" :items="categorie" clearable prepend-inner-icon="mdi-camera-iris" label="Catégorie" />
              </v-col>
              <v-col cols="6">
                <v-select v-model="editedSouscription.abonnement" :items="abonnement" clearable prepend-inner-icon="mdi-camera-control" label="Abonnement" />
              </v-col>
              <v-col cols="6">
                <v-text-field v-model="editedSouscription.prixAvecRemise" type="number" min="0" clearable prepend-inner-icon="mdi-orbit" label="Prix avec remise"/>
              </v-col>
              <v-col cols="6">
                <v-text-field v-model="editedSouscription.prixSansRemise" type="number" min="0" clearable prepend-inner-icon="mdi-orbit" label="Prix sans remise"/>
              </v-col>
              <v-col cols="12">
                <v-text-field v-model="editedSouscription.description" clearable prepend-inner-icon="mdi-text" label="Description" />
              </v-col>
              <v-col cols="6">
                <v-menu v-model="menu2" :close-on-content-click="false" :nudge-right="40" transition="scale-transition" offset-y min-width="290px">
                  <template v-slot:activator="{ on }">
                    <v-text-field v-model="editedSouscription.date_debut" prepend-inner-icon="mdi-calendar" label="Début ..." readonly v-on="on"/>
                  </template>
                  <v-date-picker v-model="editedSouscription.date_debut" @input="menu2 = false"></v-date-picker>
                </v-menu>
              </v-col>
              <v-col cols="6">
                <v-menu v-model="menu3" :close-on-content-click="false" :nudge-right="40" transition="scale-transition" offset-y min-width="290px">
                  <template v-slot:activator="{ on }">
                    <v-text-field v-model="editedSouscription.date_fin" prepend-inner-icon="mdi-calendar" label="Fin ..." readonly v-on="on"/>
                  </template>
                  <v-date-picker v-model="editedSouscription.date_fin" @input="menu3 = false"></v-date-picker>
                </v-menu>
              </v-col>
              <v-col cols="12">
                <v-select v-model="editedSouscription.action" :items="action" clearable prepend-inner-icon="mdi-power-setting" label="Action à Effectuer" />
              </v-col>
            </v-row>
            <v-row>
              <v-col cols="12">
                <v-btn color="red" dark small fab class="ml-3 white--text" @click="save">
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
    <div class="page">
      <div class="year-navigation">
        <v-btn @click="changeYear(0)" id="forTooltip" class="hover-rotate ml-1" width="30" height="30" color="#ff4e00" dark fab small>
          <v-icon small dark>mdi-chevron-left</v-icon>
        </v-btn>
        <span class="year" v-show="currentYear"> {{ currentYear }} </span>
        <v-btn @click="changeYear(1)" class="hover-rotate ml-1" width="30" height="30" color="#ff4e00" dark fab small>
          <v-icon small dark>mdi-chevron-right</v-icon>
        </v-btn>
      </div>
      <div class="gantt-container">
        <div class="gantt-header">
          <div class="gantt-month" v-for="(item, index) in month" :key="index">
            <span class="month-abb"> {{ item.abbr }} </span>
            <span class="month-name"> {{ item.name }} </span>
            <div class="month-day">
              <span class="elt-date" v-for="(n, i) in item.nbrJour" :key="i"></span>
            </div>
          </div>
        </div>
        <div class="gantt-content" v-show="toShow.length > 0" v-for="item in toShow" :key="item.id">
          <div class="gantt-name">
            <span class="gantt-name__name text-truncate">{{ item.souscription }}</span>
          </div>
          <div class="gantt-elt lines-bg">
            <div class="gantt-elt__elt text-truncate" @click="editSouscription(item)" alt="Cliquer pour modifier"> {{ item.description }} </div>
          </div>
          <div class="gantt-actions">
            <v-icon @click="editSouscription(item)" small dark color="warning" class="mx-1" style="font-size: .8em">mdi-pencil</v-icon>
            <v-icon @click="deleteSouscription(item)" small dark color="red" style="font-size: .8em">mdi-delete</v-icon>
          </div>
        </div>
        <div class="gantt-content" v-if="toShow.length === 0">
          <div class="gantt-name" style="background: transparent !important; box-shadow: none !important">
          </div>
          <div class="gantt-elt lines-bg">
            <div> <span class="alert-text">Aucun enrégistrement pour le moment</span> </div>
          </div>
          <div class="gantt-actions"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    props: {
      source: String,
    },
    data: () => ({
      currentUser: null,
      editedIndex: -1,
      dialog: false,
      e6: 1,
      currentDate: new Date(),
      currentYear: null,
      modalHide: true,
      edit: false,
      oldId: null,
      addStep: 0,
      countUpdate: 0,
      totalNbrDay: 0,
      toShow: [],
      souscriptions: [],
      menu2: false,
      menu3: false,
      categorie: [
        { text: 'TV', value: 'TV'},
        { text: 'Téléphone', value: 'Téléphone'},
        { text: 'Chauffage', value: 'Chauffage'},
        { text: 'Data', value: 'Data'},
        { text: 'Electricité', value: 'Electricité'},
        { text: 'Assurance', value: 'Assurance'},
        { text: 'Eau', value: 'Eau'},
        { text: 'Sport', value: 'Sport'}
      ],
      abonnement: [
        { text: 'SFR Adsl fixe', value: 'SFR Adsl fixe' },  
        { text: 'Total Gaz', value: 'Total Gaz' },  
        { text: 'AWS Cloud', value: 'AWS Cloud' },  
        { text: 'Energie', value: 'Energie' },  
        { text: 'AXA', value: 'AXA' },  
        { text: 'Véolia eau', value: 'Véolia eau' },  
        { text: 'Illimité', value: 'Illimité' }  
      ],
      action: [
        { text: 'Désactiver', value: 0 },
        { text: 'Modifier', value: 1 },
        { text: 'Renouveler', value: 2 }
      ],
      editedSouscription: {
        id: null,
        userId: null,
        souscription: null,
        categorie: null,
        abonnement: null,
        prixAvecRemise: null,
        prixSansRemise: null,
        description: null,
        date_debut: null,
        date_fin: null,
        cycle: null,
        action: null,
        statut: null
      },
      newSouscription: {
        id: null,
        userId: null,
        souscription: null,
        categorie: null,
        abonnement: null,
        prixAvecRemise: null,
        prixSansRemise: null,
        description: null,
        date_debut: null,
        date_fin: null,
        cycle: null,
        action: null,
        statut: null
      },
      color: [ '#764ba2', '#45a29e', '#5d001e', '#9a1750', '#d79922', '#ac3b61', '#116466', '#14a76c', '#2e1114', '#0c0032' ],
      month: [
        { name: 'Janvier', abbr: 'Jan.', nbrJour: null },
        { name: 'Février', abbr: 'Fév.', nbrJour: null },
        { name: 'Mars', abbr: 'Mars.', nbrJour: null },
        { name: 'Avril', abbr: 'Avr.', nbrJour: null },
        { name: 'Mai', abbr: 'Mai.',brJour: null },
        { name: 'Juin', abbr: 'Juin.', nbrJour: null },
        { name: 'Juillet', abbr: 'Jul.', nbrJour: null },
        { name: 'Août', abbr: 'Août.', nbrJour: null },
        { name: 'Septembre', abbr: 'Sept.', nbrJour: null },
        { name: 'Octobre', abbr: 'Oct.', nbrJour: null },
        { name: 'Novembre', abbr: 'Nov.', nbrJour: null },
        { name: 'Décembre', abbr: 'Déc.', nbrJour: null }
      ]
    }),
    watch: {
      dialog (val) {
        val || this.close()
      },
    },
    methods: {
      changeYear(id) {
        this.toShow = []
        if (id === 0) { this.currentYear--} else { this.currentYear++}
        this.souscriptions.forEach(elt => {
          if(new Date(elt.date_debut).getFullYear() === this.currentYear || new Date(elt.date_fin).getFullYear() === this.currentYear) {
            this.toShow.push(elt)
          }
        })
        this.initDraw()
      },
      initDraw () {
        for (let i = 0; i< this.toShow.length; i++) {
          this.drawGantElt(i)
        }
      },
      drawGantElt (id) {
        let parent = document.querySelector('.gantt-elt')
        let elts = document.querySelectorAll('.gantt-elt > .gantt-elt__elt')
        let elt = elts[id]
        let y_start = new Date ('1/1/' + this.currentYear)
        let t_start = new Date (this.toShow[id].date_debut)
        let t_end = new Date (this.toShow[id].date_fin)
        let t_diff = t_end.getTime() - t_start.getTime()
        let y_diff = t_start.getTime() - y_start.getTime()

        t_diff /= (1000 * 3600 * 24)
        y_diff /= (1000 * 3600 * 24)

        if (elt) {
          if (new Date(this.toShow[id].date_debut).getFullYear() < this.currentYear) {
            let temp_diff = new Date(this.toShow[id].date_fin).getTime() - new Date('1/1/' + this.currentYear).getTime()
            temp_diff /= (1000 * 3600 * 24)
            elt.style.width = (parent.clientWidth / 365) * temp_diff + 'px'
            elt.style.marginLeft = '0px'
          } else {
            elt.style.width = (parent.clientWidth / 365) * t_diff + 'px'
            elt.style.marginLeft = (parent.clientWidth / 365) * y_diff + 1 + 'px'
          }
          elt.style.background = this.color[Math.floor(Math.random() * Math.floor(9))]
        }
      },
      setYearDayNbr (year) {
        for (let i = 0; i < this.month.length; i++) {
          this.month[i].nbrJour = new Date(year, i + 1, -1).getDate() + 1
          this.totalNbrDay += this.month[i].nbrJour
        } // Algormithme pour attribuer correctement le nombre de jours en fonction de l'année en cours
      },
      msToDay (nbr) { return nbr / (1000 * 3600 * 24)},
      dayToMs (nbr) { return nbr * (1000 * 3600 * 24)},
      saveToLocal () {
        let data = JSON.stringify(this.souscriptions)
        window.localStorage.setItem('souscriptions-' + this.currentUser.username + this.currentUser.id, data)

        setTimeout(() => {
          window.location.reload()
        }, 500)
      },
      editSouscription (item) {
        this.editedIndex = this.souscriptions.indexOf(item)
        this.editedSouscription = Object.assign({}, item)
        this.dialog = true
      },
      deleteSouscription (item) {
        const index = this.souscriptions.indexOf(item)
        if (confirm('Êtes vous sûr de vouloir supprimer cette souscription ?')) {
          this.souscriptions.splice(index, 1)
          this.saveToLocal()
        }
      },
      close () {
        this.dialog = false
        setTimeout(() => {
          this.editedSouscription = Object.assign({}, this.newSouscription)
          this.editedIndex = -1
          this.e6 = 1
          this.initDraw()
        }, 300)
      },
      save () {
        let ecartDate = Math.round(this.msToDay(new Date(this.editedSouscription.date_end) - new Date()))

        if (ecartDate < 0) {
          this.editedSouscription.statut = 0 // Déjà Fini
        } else {
          if (ecartDate <= 14) {
            this.editedSouscription.statut = 1 // Fini dans deux semaines
          } else {
            this.editedSouscription.statut = 2 // Encore loin
          }
        }
        if (this.editedIndex > -1) {
          Object.assign(this.souscriptions[this.editedIndex], this.editedSouscription)
        } else {
          this.souscriptions.push({
            id: Math.floor(new Date () * Math.random() * Math.floor(9)),
            userId: this.currentUser.id,
            souscription: this.editedSouscription.souscription,
            categorie: this.editedSouscription.categorie,
            abonnement: this.editedSouscription.abonnement,
            prixAvecRemise: this.editedSouscription.prixAvecRemise,
            prixSansRemise: this.editedSouscription.prixSansRemise,
            description: this.editedSouscription.description,
            date_debut: this.editedSouscription.date_debut,
            date_fin: this.editedSouscription.date_fin,
            cycle: this.editedSouscription.cycle,
            action: this.editedSouscription.action,
            statut: this.editedSouscription.statut})
        }
        this.saveToLocal()
        this.close()
      }
    },
    mounted () {
      this.currentUser = JSON.parse(window.localStorage.getItem('alpha-user-connected'))
      this.currentYear = this.currentDate.getFullYear()
      this.setYearDayNbr(this.currentDate.getFullYear())
      if (window.localStorage.getItem('souscriptions-' + this.currentUser.username + this.currentUser.id) !== null && window.localStorage.getItem('souscriptions-' + this.currentUser.username + this.currentUser.id).length > 0) {
        let tmpSouscriptions = JSON.parse(window.localStorage.getItem('souscriptions-' + this.currentUser.username + this.currentUser.id))
        tmpSouscriptions.forEach(souscription => {
          if (souscription.userId === this.currentUser.id) {
            this.souscriptions.push(souscription)
          }
        })
        this.souscriptions.forEach(elt => { 
          if(new Date(elt.date_debut).getFullYear() === this.currentYear || new Date(elt.date_fin).getFullYear() === this.currentYear) {
            this.toShow.push(elt)
          }
        })
      }
    },
    updated () {
      let counter = 0
      if (counter === 0) {
        this.initDraw()
        counter++
      }
    }
  }
</script>

<style lang="css">
.tippy {
  position: absolute;
  top: 0;
  left: 0;
  max-width: 350px;
  background: rgb(22, 22, 22, .9);
  padding: 10px 15px;
  font-size: .815em;
  color: #fff;
  opacity: 0;
  border-radius: 20px;
  transform: translateX(-20px);
  transform-origin: 50% 100%;
  transition: opacity .3s, transform .3s, border-radius .3s;
  -webkit-border-radius: 20px;
  -moz-border-radius: 20px;
  -ms-border-radius: 20px;
  -o-border-radius: 20px;
  -webkit-transition: opacity .3s, transform .3s, border-radius .3s;
  -moz-transition: opacity .3s, transform .3s, border-radius .3s;
  -ms-transition: opacity .3s, transform .3s, border-radius .3s;
  -o-transition: opacity .3s, transform .3s, border-radius .3s;
  -webkit-transform: translateX(-20px);
  -moz-transform: translateX(-20px);
  -ms-transform: translateX(-20px);
  -o-transform: translateX(-20px);
}

.tippy::after {
  content: "";
  position: absolute;
  top: 100%;
  left: 50%;
  margin-left: -6px;
  border-left: 6px solid transparent;
  border-right: 6px solid transparent;
  border-top: 6px solid #222;
}

.tippy.visible {
  display: block !important;
  opacity: 1;
  border-radius: 3px;
  transform: translateX(0);
  -webkit-transform: translateX(0);
  -moz-transform: translateX(0);
  -ms-transform: translateX(0);
  -o-transform: translateX(0);
  -webkit-border-radius: 3px;
  -moz-border-radius: 3px;
  -ms-border-radius: 3px;
  -o-border-radius: 3px;
}

.hided { display: none;}
</style>
