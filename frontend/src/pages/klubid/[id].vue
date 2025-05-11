<template>
  <v-container>
    <v-row v-if="club">
      <v-col>
        <h1 class="mb-2"><a href="/klubid">Klubid</a> / {{ club.name }}</h1>
      </v-col>
    </v-row>
    <div v-if="club">
      <v-row dense>
        <v-col cols="12" md="6" lg="4">
          <v-card outlined>
            <v-card-title>{{ club.membersCount }}</v-card-title>
            <v-card-text>
              MÄNGIJAID
            </v-card-text>
          </v-card>
        </v-col>

        <v-col cols="12" md="6" lg="4">
          <v-card outlined>
            <v-card-title>{{ club.averageRating }}</v-card-title>
            <v-card-text>
              KESKMINE REITING
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>

      <v-row cols="12" md="8">
        <v-col>
          <v-divider :thickness="3"></v-divider>
        </v-col>
      </v-row>

      <v-row>
        <v-col cols="12" md="6" lg="4">
          <ModifyClubForm
            :is-update="true"
            :club-id="clubId"
            @club-updated="fetchClubData"
          />
        </v-col>
        <v-col cols="12" md="6">
        <h2>TOP mängijad</h2>
        <v-row>
          <v-col cols="10" v-for="(player, index) in clubTopPlayers" :key="player.isik">
            <v-card>
              <v-card-title>
                <v-row align="center">
                  <v-col cols="8">
                    <v-chip :color="getChipColor(index)" class="ma-2" label>{{ index + 1 }}</v-chip>
                    {{ player.isik }}
                  </v-col>
                  <v-col cols="4" class="text-right points">
                    {{ parseFloat(player.punktisumma).toFixed(1) }}
                  </v-col>
                </v-row>
              </v-card-title>
            </v-card>
          </v-col>
        </v-row>
      </v-col>
      </v-row>

      <v-row cols="12" md="8">
        <v-col>
          <v-divider :thickness="3"></v-divider>
        </v-col>
      </v-row>

      <v-row>
        <v-col>
          <PlayersSearchTable :club-id="clubId" />
        </v-col>
      </v-row>

    </div>
    <div v-else>
      <h2>Klubi ei leitud</h2>
      <p>Vabandame, antud klubi ei eksisteeri või on andmed puudulikud.</p>
      <v-btn color="primary"
             @click="this.$router.push('/klubid')">
        Tagasi klubide lehele
      </v-btn>
    </div>
  </v-container>
</template>

<script>
import {fetchClubById} from "@/wrapper/clubsApiWrapper.js";
import {fetchClubTopPlayers} from "@/wrapper/clubsApiWrapper.js";
import PlayersSearchTable from "@/components/clubs/PlayersSearchTable.vue";
import AddClubDialog from "@/components/clubs/AddClubDialog.vue";
import ModifyClubForm from "@/components/clubs/ModifyClubForm.vue";

export default {
  name: 'ClubDetailsPage',
  components: {
    ModifyClubForm,
    AddClubDialog,
    PlayersSearchTable
  },
  data() {
    return {
      club: null,
      clubId: null,
      showModifyClubDialog: false,
      clubTopPlayers: []
    }
  },
  methods: {
    getChipColor(index) {
      switch (index) {
        case 0:
          return 'gold';
        case 1:
          return 'silver';
        case 2:
          return 'bronze';
        default:
          return 'primary';
      }
    },
    loadData() {
      this.fetchClubData()
      this.fetchClubTopPlayers()
    },
    async fetchClubTopPlayers() {
      this.clubTopPlayers = await fetchClubTopPlayers(this.clubId)
    },
    async fetchClubData() {
      this.club = await fetchClubById(this.clubId)
    },
    openModifyClubDialog() {
      this.showModifyClubDialog = true;
    },
    updateShowModifyDialog(value) {
      this.showModifyClubDialog = value;
    },
  },
  created() {
    this.clubId = this.$route.params.id;
    this.$watch(
      () => this.$route.params.id,
      this.loadData,
      {immediate: true}
    )
  }
}
</script>

<style scoped>
.club-title {
  margin-bottom: 1.5rem;
  font-size: 2rem;
  font-weight: bold;
}

</style>
