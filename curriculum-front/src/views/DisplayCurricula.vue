<template>
  <v-row no-gutters class="display-curricula-page">
    <v-col
      md="6"
      offset-md="3"
      sm="8"
      offset-sm="2"
    >
      <div class="page-header">
        <h1>Curricula</h1>
        <v-btn v-if="user.token" @click="$router.push('/curricula/create')">
          Create New
        </v-btn>
      </div>

      <v-tabs v-model="currentTab">
        <v-tab>My Curricula</v-tab>
        <v-tab>All Curricula</v-tab>
      </v-tabs>

      <div class="curricula-list">
        <v-card
          class="curriculum-card"
          outlined
          v-for="curriculum in curricula"
          :key="curriculum._id"
        >
          <v-card-title class="headline">
            <router-link :to="`/curricula/${curriculum._id}`">{{ curriculum.name }}</router-link>
          </v-card-title>
          
          <v-card-subtitle>
            {{ curriculum.description }}
          </v-card-subtitle>
          
          <v-card-text>
            <v-progress-linear
              :value="retrieveCompleted(curriculum._id)"
              color="blue-grey"
              height="25"
            >
              <template v-slot="{ value }">
                <strong>{{ value }}%</strong>
              </template>
            </v-progress-linear>
          </v-card-text>
        </v-card>
      </div>
    </v-col>
  </v-row>
</template>

<script>
import { mapState, mapActions } from 'vuex'

export default {
  name: 'DisplayCurricula',
  data() {
    return {
      ratioCompleted: 35,
      currentTab: 0
    }
  },
  computed: {
    ...mapState(['curricula', 'completeCounts']),
    ...mapState('auth', ['user'])
  },
  watch: {
    currentTab(val) {
      if (val == 0) {
        this.getUserCurricula(this.user.id)
      } else {
        this.getCurricula()
      }
    }
  },
  methods: {
    ...mapActions(['getCurricula', 'getUserCurricula', 'countAllCompleted']),
    retrieveCompleted(id) {
      if (this.curricula) {
        const totals = this.completeCounts.find((obj) => {
        return obj.id === id
        })
        return Math.floor((totals.numberCompleted / totals.totalNumber) * 100)
      }
      return 0
    }
  },
  mounted() {
    this.getUserCurricula(this.user.id)
  },
  created() {
    this.countAllCompleted()
  }
}
</script>
