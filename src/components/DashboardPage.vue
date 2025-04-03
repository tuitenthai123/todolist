<template>
  <v-container>
    <v-row>
      <v-col>
        <h1>Welcome to Dashboard</h1>
        <v-btn color="primary" @click="handleLogout">Logout</v-btn>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
export default {
  name: 'DashboardPage',
  methods: {
    handleLogout() {
      this.$store.commit('SET_LOGIN_STATUS', false)
      localStorage.removeItem('token')
      this.$router.push('/')
    }
  }
}
</script>