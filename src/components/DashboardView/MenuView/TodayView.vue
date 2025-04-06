<script>

export default {
  data() {
    return {
      picker: (new Date(Date.now() - (new Date()).getTimezoneOffset() * 60000)).toISOString().substr(0, 10),
      itemssort: {
        category: ['All', 'Work', 'Personal', 'Health', 'Finance', 'Study'],
        priority: ['All', 'High', 'Medium', 'Low'],
        sortBy: ['Due date', 'Priority', 'Alphabetical'],
      },
      itemsdetail: {
        summary: {
          taskstatus: [
            {
              numtask: 2,
              state: "Completed",
              color: "primary"
            },
            {
              numtask: 7,
              state: "Pending",
              color: "red"
            }
          ],
          progressstatus: {
            processing: 22,
          }
        }

      }
    }
  },
}

</script>

<template>
  <div class="pa-3">
    <v-row>
      <v-col cols="12" md="9">
        <v-list>
          <v-list-item>
            <div class="d-flex justify-space-between" style="width: 100%">
              <div class="d-flex flex-column align-start">
                <span class="text-h4 font-weight-medium">Today's Tasks</span>
                <span class="grey--text font-weight-medium text-subtitle-1">{{
                    new Date().toLocaleDateString('en-EN', {
                      weekday: 'long',
                      year: 'numeric',
                      month: 'long',
                      day: 'numeric'
                    })
                  }}</span>
              </div>
              <div>
                <v-btn color="primary">
                  <v-icon>mdi-plus</v-icon>
                  TASK
                </v-btn>
              </div>
            </div>
          </v-list-item>
          <v-list-item class="mt-5">
            <v-card class="rounded-lg pa-4" elevation="2" style="width: 100%">
              <v-row align="center" justify="center">
                <v-col cols="12" sm="4">
                  <v-select
                      outlined
                      label="Category"
                      :items="itemssort?.category"
                      :value="itemssort?.category[0]"
                      hide-details
                  ></v-select>
                </v-col>
                <v-col cols="12" sm="4">
                  <v-select
                      outlined
                      label="Priority"
                      :items="itemssort?.priority"
                      :value="itemssort?.priority[0]"
                      hide-details
                  ></v-select>
                </v-col>
                <v-col cols="12" sm="4">
                  <v-select
                      outlined
                      label="Sort by"
                      :items="itemssort?.sortBy"
                      :value="itemssort?.sortBy[0]"
                      hide-details
                  ></v-select>
                </v-col>
              </v-row>
            </v-card>
          </v-list-item>
        </v-list>
      </v-col>

      <!--   Detail bar   -->
      <v-col cols="12" md="3" style="width: 100%" class="hidden-sm-and-down">
        <v-card class="pa-2 flex-column align-center" elevation="2">
          <v-list>
            <v-list-item>
              <div class="d-flex align-center ">
                <v-icon color="primary" class="mr-2">mdi-chart-box</v-icon>
                <span class="font-weight-medium text-h6">Summary</span>
              </div>
            </v-list-item>

            <v-list-item>
              <v-row align="center">
                <v-col cols="12" sm="6" v-for="(item,index) in itemsdetail?.summary?.taskstatus" :key="index">
                  <v-card class="d-flex flex-column align-center justify-center my-5 py-5 rounded-lg">
                    <span :class="item?.color + '--text font-weight-bold text-h4'">{{ item?.numtask }}</span>
                    <span class="grey--text text-caption">{{ item?.state }}</span>
                  </v-card>
                </v-col>
              </v-row>
            </v-list-item>

            <v-list-item>
              <div class="d-flex flex-column" style="width: 100%">
                <div class="d-flex align-center justify-space-between text-subtitle-1 blue-grey--text">
                  <span>Progress</span>
                  <span>{{ itemsdetail?.summary?.progressstatus.processing }}%</span>
                </div>
                <div class="mt-2">
                  <v-progress-linear
                      :value="itemsdetail?.summary?.progressstatus.processing"
                      rounded
                      height="8"
                  ></v-progress-linear>
                </div>
              </div>
            </v-list-item>
          </v-list>
        </v-card>

        <!--    Calendar      -->
        <v-card class="pa-2 flex-column align-center mt-5" elevation="2">
          <v-list>
            <v-list-item>
              <div class="d-flex align-center ">
                <v-icon color="primary" class="mr-2">mdi-calendar</v-icon>
                <span class="font-weight-medium text-h6">Calendar</span>
              </div>
            </v-list-item>
            <v-list-item>
              <v-row align="center" justify="center" class="mt-2">
                <v-date-picker
                    v-model="picker"
                    width="auto"
                ></v-date-picker>
              </v-row>
            </v-list-item>
          </v-list>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<style scoped>

</style>