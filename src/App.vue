<template>
  <v-app>
    <v-main class="gradient">
      <v-container fluid fill-height>
        <v-row align="center" justify="center">
          <v-col cols="12" sm="8" md="6" lg="4">
            <v-card class="mx-auto rounded-lg" elevation="8">
              <v-card-title class="d-flex justify-center pt-8">
                <span class="text-h4 font-weight-bold">LOGIN</span>
              </v-card-title>

              <v-card-text class="px-8 pt-8">
                <v-text-field clearable label="Username" prepend-icon="mdi-account" variant="underlined" hide-details
                  class="mb-6"></v-text-field>

                <v-text-field clearable label="Password" prepend-icon="mdi-lock" variant="underlined"
                  :append-icon="showPassword ? 'mdi-eye' : 'mdi-eye-off'" :type="showPassword ? 'text' : 'password'"
                  @click:append="showPassword = !showPassword" hide-details class="mb-6"></v-text-field>

                <v-row no-gutters class="mb-6" justify="space-between" align="center">
                  <v-checkbox dense label="Remember me" hide-details></v-checkbox>
                  <v-btn text color="primary" class="text-none">Forgot password?</v-btn>
                </v-row>

                <v-btn block color="primary" height="44" rounded elevation="2" class="text-h6 mb-8"
                  @click="snackbar = true">
                  LOGIN
                </v-btn>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
      <v-snackbar v-model="snackbar">
        {{ text }}

        <template v-slot:action="{ attrs }">
          <v-btn color="pink" text v-bind="attrs" @click="snackbar = false">
            Close
          </v-btn>
        </template>
      </v-snackbar>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data: () => ({
    showPassword: false,
    snackbar: false,
    text: `Đăng nhập thành công`,
  })
}
</script>

<style>
.gradient {
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
}
</style>