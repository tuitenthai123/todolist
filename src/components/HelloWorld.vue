<template>
  <v-app :theme="theme">
    <v-navigation-drawer
        v-model="drawer"
        app
        class="elevation-4"
    >
      <v-list-item class="my-4">
        <v-list-item-avatar size="40">
          <v-img src="https://randomuser.me/api/portraits/men/78.jpg"></v-img>
        </v-list-item-avatar>
        <v-list-item-content>
          <v-list-item-title class="text-h6">Nguyễn Văn A</v-list-item-title>
          <v-list-item-subtitle>Premium User</v-list-item-subtitle>
        </v-list-item-content>
        <v-btn icon @click.stop="drawer = !drawer">
          <v-icon>mdi-chevron-left</v-icon>
        </v-btn>
      </v-list-item>

      <v-divider></v-divider>

      <v-list
          dense
          nav
      >
        <v-list-item
            v-for="item in menuItems"
            :key="item.title"
            :to="item.to"
            link
            :class="{ 'primary--text': currentView === item.title.toLowerCase() }"
            @click="currentView = item.title.toLowerCase()"
        >
          <v-list-item-icon>
            <v-icon :color="currentView === item.title.toLowerCase() ? 'primary' : ''">{{ item.icon }}</v-icon>
          </v-list-item-icon>

          <v-list-item-content>
            <v-list-item-title>{{ item.title }}</v-list-item-title>
          </v-list-item-content>

          <v-chip
              v-if="item.count"
              small
              :color="item.color"
              text-color="white"
              class="ml-2"
          >
            {{ item.count }}
          </v-chip>
        </v-list-item>
      </v-list>

      <template v-slot:append>
        <div class="pa-4">
          <v-divider class="mb-4"></v-divider>
          <v-list-item link @click="toggleTheme">
            <v-list-item-icon>
              <v-icon>{{ theme === 'light' ? 'mdi-weather-night' : 'mdi-white-balance-sunny' }}</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>{{ theme === 'light' ? 'Dark Mode' : 'Light Mode' }}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
          <v-list-item link>
            <v-list-item-icon>
              <v-icon>mdi-cog</v-icon>
            </v-list-item-icon>
            <v-list-item-content>
              <v-list-item-title>Settings</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </div>
      </template>
    </v-navigation-drawer>

    <v-app-bar
        app
        elevate-on-scroll
        :color="theme === 'light' ? 'white' : ''"
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
      <v-toolbar-title class="text-h5 font-weight-bold primary--text">
        <span class="hidden-sm-and-down">TaskFlow</span>
        <v-icon large color="primary" class="hidden-md-and-up">mdi-check-decagram</v-icon>
      </v-toolbar-title>
      <v-spacer></v-spacer>

      <v-text-field
          v-model="search"
          prepend-inner-icon="mdi-magnify"
          label="Search tasks..."
          hide-details
          filled
          rounded
          dense
          class="mx-4 hidden-sm-and-down search-field"
          style="max-width: 300px"
      ></v-text-field>

      <v-btn icon class="hidden-md-and-up">
        <v-icon>mdi-magnify</v-icon>
      </v-btn>

      <v-btn icon>
        <v-icon>mdi-bell</v-icon>
        <v-badge
            color="error"
            content="3"
            offset-x="10"
            offset-y="10"
            dot
        ></v-badge>
      </v-btn>

      <v-menu
          left
          bottom
      >
        <template v-slot:activator="{ on, attrs }">
          <v-btn
              icon
              v-bind="attrs"
              v-on="on"
          >
            <v-icon>mdi-dots-vertical</v-icon>
          </v-btn>
        </template>

        <v-list>
          <v-list-item
              v-for="(item, i) in dropdownItems"
              :key="i"
              @click="() => {}"
          >
            <v-list-item-icon>
              <v-icon v-text="item.icon"></v-icon>
            </v-list-item-icon>
            <v-list-item-title v-text="item.title"></v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>
    </v-app-bar>

    <v-main>
      <v-container fluid class="fill-height">
        <v-row>
          <v-col cols="12" md="8">
            <!-- Header Section -->
            <div class="d-flex align-center justify-space-between mb-6">
              <div>
                <h1 class="text-h4 font-weight-bold">{{ viewTitle }}</h1>
                <p class="text-subtitle-1 grey--text">{{ new Date().toLocaleDateString('vi-VN', { weekday: 'long', year: 'numeric', month: 'long', day: 'numeric' }) }}</p>
              </div>
              <v-btn
                  color="primary"
                  dark
                  rounded
                  elevation="2"
                  @click="dialog = true"
              >
                <v-icon left>mdi-plus</v-icon>
                Add Task
              </v-btn>
            </div>

            <!-- Task Filters -->
            <v-card class="mb-6 rounded-lg" elevation="2">
              <v-card-text>
                <v-row align="center">
                  <v-col cols="12" sm="4">
                    <v-select
                        v-model="filterCategory"
                        :items="categories"
                        label="Category"
                        outlined
                        dense
                        hide-details
                    ></v-select>
                  </v-col>
                  <v-col cols="12" sm="4">
                    <v-select
                        v-model="filterPriority"
                        :items="priorities"
                        label="Priority"
                        outlined
                        dense
                        hide-details
                    ></v-select>
                  </v-col>
                  <v-col cols="12" sm="4">
                    <v-select
                        v-model="sortBy"
                        :items="sortOptions"
                        label="Sort by"
                        outlined
                        dense
                        hide-details
                    ></v-select>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>

            <!-- Tasks List -->
            <div class="tasks-container">
              <v-slide-y-transition group>
                <v-card
                    v-for="task in filteredTasks"
                    :key="task.id"
                    class="mb-4 task-card rounded-lg"
                    :class="{ 'completed-task': task.completed }"
                    elevation="2"
                >
                  <v-card-text class="py-4">
                    <v-row align="center" no-gutters>
                      <v-col cols="auto" class="mr-3">
                        <v-checkbox
                            v-model="task.completed"
                            color="primary"
                            hide-details
                            class="ma-0 pa-0"
                        ></v-checkbox>
                      </v-col>
                      <v-col>
                        <div class="d-flex flex-column">
                          <div class="d-flex align-center">
                            <span
                                class="text-subtitle-1 font-weight-medium"
                                :class="{ 'text-decoration-line-through': task.completed }"
                            >
                              {{ task.title }}
                            </span>
                            <v-chip
                                x-small
                                :color="getPriorityColor(task.priority)"
                                text-color="white"
                                class="ml-2"
                            >
                              {{ task.priority }}
                            </v-chip>
                            <v-chip
                                x-small
                                color="primary"
                                outlined
                                class="ml-2"
                            >
                              {{ task.category }}
                            </v-chip>
                          </div>
                          <span
                              class="text-caption grey--text mt-1"
                              :class="{ 'text-decoration-line-through': task.completed }"
                          >
                            {{ task.description }}
                          </span>
                        </div>
                      </v-col>
                      <v-col cols="auto">
                        <div class="d-flex align-center">
                          <v-chip
                              small
                              :color="isOverdue(task.dueDate) ? 'error' : 'grey lighten-3'"
                              :text-color="isOverdue(task.dueDate) ? 'white' : ''"
                              class="mr-2"
                          >
                            <v-icon left small>mdi-calendar</v-icon>
                            {{ formatDate(task.dueDate) }}
                          </v-chip>
                          <v-menu
                              left
                              bottom
                          >
                            <template v-slot:activator="{ on, attrs }">
                              <v-btn
                                  icon
                                  small
                                  v-bind="attrs"
                                  v-on="on"
                              >
                                <v-icon>mdi-dots-vertical</v-icon>
                              </v-btn>
                            </template>

                            <v-list dense>
                              <v-list-item @click="editTask(task)">
                                <v-list-item-icon>
                                  <v-icon>mdi-pencil</v-icon>
                                </v-list-item-icon>
                                <v-list-item-title>Edit</v-list-item-title>
                              </v-list-item>
                              <v-list-item @click="deleteTask(task.id)">
                                <v-list-item-icon>
                                  <v-icon>mdi-delete</v-icon>
                                </v-list-item-icon>
                                <v-list-item-title>Delete</v-list-item-title>
                              </v-list-item>
                            </v-list>
                          </v-menu>
                        </div>
                      </v-col>
                    </v-row>
                  </v-card-text>
                </v-card>
              </v-slide-y-transition>

              <v-card v-if="filteredTasks.length === 0" class="text-center pa-6 rounded-lg" elevation="0">
                <v-img
                    src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg"
                    height="200"
                    contain
                ></v-img>
                <v-card-title class="justify-center">No tasks found</v-card-title>
                <v-card-subtitle>Try changing your filters or add a new task</v-card-subtitle>
                <v-btn color="primary" text @click="dialog = true">Add a new task</v-btn>
              </v-card>
            </div>
          </v-col>

          <v-col cols="12" md="4" class="hidden-sm-and-down">
            <!-- Summary Card -->
            <v-card class="mb-6 rounded-lg" elevation="2">
              <v-card-title>
                <v-icon left color="primary">mdi-chart-box</v-icon>
                Summary
              </v-card-title>
              <v-card-text>
                <v-row>
                  <v-col cols="6">
                    <v-card class="rounded-lg" outlined>
                      <v-card-text class="text-center">
                        <div class="text-h4 font-weight-bold primary--text">{{ completedTasksCount }}</div>
                        <div class="text-caption grey--text">Completed</div>
                      </v-card-text>
                    </v-card>
                  </v-col>
                  <v-col cols="6">
                    <v-card class="rounded-lg" outlined>
                      <v-card-text class="text-center">
                        <div class="text-h4 font-weight-bold error--text">{{ pendingTasksCount }}</div>
                        <div class="text-caption grey--text">Pending</div>
                      </v-card-text>
                    </v-card>
                  </v-col>
                </v-row>

                <div class="mt-4">
                  <div class="d-flex justify-space-between align-center mb-1">
                    <span class="text-body-2">Progress</span>
                    <span class="text-body-2">{{ progressPercentage }}%</span>
                  </div>
                  <v-progress-linear
                      :value="progressPercentage"
                      color="primary"
                      height="10"
                      rounded
                  ></v-progress-linear>
                </div>
              </v-card-text>
            </v-card>

            <!-- Calendar Card -->
            <v-card class="mb-6 rounded-lg" elevation="2">
              <v-card-title>
                <v-icon left color="primary">mdi-calendar</v-icon>
                Calendar
              </v-card-title>
              <v-card-text>
                <v-date-picker
                    v-model="selectedDate"
                    full-width
                    class="mt-2"
                    :events="taskDueDates"
                    event-color="primary"
                ></v-date-picker>
              </v-card-text>
            </v-card>

            <!-- Category Distribution -->
            <v-card class="rounded-lg" elevation="2">
              <v-card-title>
                <v-icon left color="primary">mdi-tag-multiple</v-icon>
                Categories
              </v-card-title>
              <v-card-text>
                <v-chip-group column>
                  <v-chip
                      v-for="(count, category) in categoryDistribution"
                      :key="category"
                      :color="getCategoryColor(category)"
                      text-color="white"
                      class="mr-2 mb-2"
                  >
                    {{ category }} ({{ count }})
                  </v-chip>
                </v-chip-group>
              </v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>

    <!-- Add/Edit Task Dialog -->
    <v-dialog
        v-model="dialog"
        max-width="600px"
    >
      <v-card class="rounded-lg">
        <v-card-title>
          <span class="text-h5">{{ editedIndex === -1 ? 'New Task' : 'Edit Task' }}</span>
          <v-spacer></v-spacer>
          <v-btn
              icon
              @click="closeDialog"
          >
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12">
                <v-text-field
                    v-model="editedItem.title"
                    label="Task title*"
                    required
                    outlined
                    dense
                ></v-text-field>
              </v-col>
              <v-col cols="12">
                <v-textarea
                    v-model="editedItem.description"
                    label="Description"
                    outlined
                    auto-grow
                    rows="3"
                    row-height="15"
                    dense
                ></v-textarea>
              </v-col>
              <v-col cols="12" sm="6">
                <v-select
                    v-model="editedItem.category"
                    :items="categories"
                    label="Category*"
                    required
                    outlined
                    dense
                ></v-select>
              </v-col>
              <v-col cols="12" sm="6">
                <v-select
                    v-model="editedItem.priority"
                    :items="priorities"
                    label="Priority*"
                    required
                    outlined
                    dense
                ></v-select>
              </v-col>
              <v-col cols="12">
                <v-menu
                    ref="dateMenu"
                    v-model="dateMenu"
                    :close-on-content-click="false"
                    :return-value.sync="editedItem.dueDate"
                    transition="scale-transition"
                    offset-y
                    min-width="auto"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                        v-model="editedItem.dueDate"
                        label="Due date"
                        prepend-icon="mdi-calendar"
                        readonly
                        outlined
                        dense
                        v-bind="attrs"
                        v-on="on"
                    ></v-text-field>
                  </template>
                  <v-date-picker
                      v-model="editedItem.dueDate"
                      no-title
                      scrollable
                  >
                    <v-spacer></v-spacer>
                    <v-btn
                        text
                        color="primary"
                        @click="dateMenu = false"
                    >
                      Cancel
                    </v-btn>
                    <v-btn
                        text
                        color="primary"
                        @click="$refs.dateMenu.save(editedItem.dueDate)"
                    >
                      OK
                    </v-btn>
                  </v-date-picker>
                </v-menu>
              </v-col>
            </v-row>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
              color="grey darken-1"
              text
              @click="closeDialog"
          >
            Cancel
          </v-btn>
          <v-btn
              color="primary"
              @click="saveTask"
          >
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <!-- Floating Action Button for Mobile -->
    <v-btn
        fab
        large
        color="primary"
        fixed
        right
        bottom
        class="mb-6 mr-6 hidden-md-and-up"
        @click="dialog = true"
    >
      <v-icon>mdi-plus</v-icon>
    </v-btn>
  </v-app>
</template>

<script>
export default {
  name: 'TodoApp',

  data: () => ({
    theme: 'light',
    drawer: null,
    currentView: 'today',
    search: '',
    dialog: false,
    dateMenu: false,
    selectedDate: new Date().toISOString().substr(0, 10),

    // Filters
    filterCategory: 'All',
    filterPriority: 'All',
    sortBy: 'dueDate',

    // Edit dialog
    editedIndex: -1,
    editedItem: {
      id: '',
      title: '',
      description: '',
      category: '',
      priority: 'Medium',
      dueDate: new Date().toISOString().substr(0, 10),
      completed: false
    },
    defaultItem: {
      id: '',
      title: '',
      description: '',
      category: '',
      priority: 'Medium',
      dueDate: new Date().toISOString().substr(0, 10),
      completed: false
    },

    // Menu items
    menuItems: [
      { title: 'Today', icon: 'mdi-calendar-today', to: '/today', count: 5, color: 'primary' },
      { title: 'Upcoming', icon: 'mdi-calendar-arrow-right', to: '/upcoming', count: 8, color: 'info' },
      { title: 'Important', icon: 'mdi-star', to: '/important', count: 3, color: 'warning' },
      { title: 'Completed', icon: 'mdi-check-circle', to: '/completed' },
      { title: 'All Tasks', icon: 'mdi-format-list-bulleted', to: '/all' },
    ],

    dropdownItems: [
      { title: 'Export Tasks', icon: 'mdi-export' },
      { title: 'Import Tasks', icon: 'mdi-import' },
      { title: 'Print', icon: 'mdi-printer' },
      { title: 'Help', icon: 'mdi-help-circle' }
    ],

    // Task data
    tasks: [
      {
        id: '1',
        title: 'Complete project proposal',
        description: 'Finish the draft and send it to the client for review',
        category: 'Work',
        priority: 'High',
        dueDate: '2025-04-05',
        completed: false
      },
      {
        id: '2',
        title: 'Buy groceries',
        description: 'Milk, eggs, bread, fruits, and vegetables',
        category: 'Personal',
        priority: 'Medium',
        dueDate: '2025-04-04',
        completed: true
      },
      {
        id: '3',
        title: 'Schedule dentist appointment',
        description: 'Call Dr. Nguyen for a check-up',
        category: 'Health',
        priority: 'Medium',
        dueDate: '2025-04-10',
        completed: false
      },
      {
        id: '4',
        title: 'Pay electricity bill',
        description: 'Due by the end of the week',
        category: 'Finance',
        priority: 'High',
        dueDate: '2025-04-07',
        completed: false
      },
      {
        id: '5',
        title: 'Call mom',
        description: 'Ask about the family reunion',
        category: 'Personal',
        priority: 'Low',
        dueDate: '2025-04-04',
        completed: false
      },
      {
        id: '6',
        title: 'Prepare presentation',
        description: 'For the team meeting on Friday',
        category: 'Work',
        priority: 'High',
        dueDate: '2025-04-06',
        completed: false
      },
      {
        id: '7',
        title: 'Go for a run',
        description: '5km around the park',
        category: 'Health',
        priority: 'Medium',
        dueDate: '2025-04-04',
        completed: true
      },
      {
        id: '8',
        title: 'Read book chapter',
        description: 'Chapter 5 of "Atomic Habits"',
        category: 'Personal',
        priority: 'Low',
        dueDate: '2025-04-08',
        completed: false
      }
    ],

    // Options
    categories: ['All', 'Work', 'Personal', 'Health', 'Finance', 'Study'],
    priorities: ['All', 'High', 'Medium', 'Low'],
    sortOptions: [
      { text: 'Due Date', value: 'dueDate' },
      { text: 'Priority', value: 'priority' },
      { text: 'Alphabetical', value: 'title' }
    ]
  }),

  computed: {
    viewTitle() {
      switch (this.currentView) {
        case 'today':
          return 'Today\'s Tasks';
        case 'upcoming':
          return 'Upcoming Tasks';
        case 'important':
          return 'Important Tasks';
        case 'completed':
          return 'Completed Tasks';
        case 'all tasks':
          return 'All Tasks';
        default:
          return 'Tasks';
      }
    },

    filteredTasks() {
      let filtered = [...this.tasks];

      // Filter by view
      if (this.currentView === 'today') {
        const today = new Date().toISOString().substr(0, 10);
        filtered = filtered.filter(task => task.dueDate === today);
      } else if (this.currentView === 'upcoming') {
        const today = new Date().toISOString().substr(0, 10);
        filtered = filtered.filter(task => task.dueDate > today && !task.completed);
      } else if (this.currentView === 'important') {
        filtered = filtered.filter(task => task.priority === 'High' && !task.completed);
      } else if (this.currentView === 'completed') {
        filtered = filtered.filter(task => task.completed);
      }

      // Filter by search
      if (this.search) {
        const searchLower = this.search.toLowerCase();
        filtered = filtered.filter(task =>
            task.title.toLowerCase().includes(searchLower) ||
            task.description.toLowerCase().includes(searchLower)
        );
      }

      // Filter by category
      if (this.filterCategory !== 'All') {
        filtered = filtered.filter(task => task.category === this.filterCategory);
      }

      // Filter by priority
      if (this.filterPriority !== 'All') {
        filtered = filtered.filter(task => task.priority === this.filterPriority);
      }

      // Sort tasks
      if (this.sortBy === 'dueDate') {
        filtered.sort((a, b) => new Date(a.dueDate) - new Date(b.dueDate));
      } else if (this.sortBy === 'priority') {
        const priorityOrder = { 'High': 1, 'Medium': 2, 'Low': 3 };
        filtered.sort((a, b) => priorityOrder[a.priority] - priorityOrder[b.priority]);
      } else if (this.sortBy === 'title') {
        filtered.sort((a, b) => a.title.localeCompare(b.title));
      }

      return filtered;
    },

    completedTasksCount() {
      return this.tasks.filter(task => task.completed).length;
    },

    pendingTasksCount() {
      return this.tasks.filter(task => !task.completed).length;
    },

    progressPercentage() {
      if (this.tasks.length === 0) return 0;
      return Math.round((this.completedTasksCount / this.tasks.length) * 100);
    },

    taskDueDates() {
      return this.tasks.map(task => task.dueDate);
    },

    categoryDistribution() {
      const distribution = {};
      this.tasks.forEach(task => {
        if (!distribution[task.category]) {
          distribution[task.category] = 0;
        }
        distribution[task.category]++;
      });
      return distribution;
    }
  },

  methods: {
    toggleTheme() {
      this.theme = this.theme === 'light' ? 'dark' : 'light';
    },

    formatDate(date) {
      if (!date) return '';
      const [year, month, day] = date.split('-');
      return `${day}/${month}/${year}`;
    },

    isOverdue(dueDate) {
      if (!dueDate) return false;
      const today = new Date().toISOString().substr(0, 10);
      return dueDate < today && !this.tasks.find(t => t.dueDate === dueDate)?.completed;
    },

    getPriorityColor(priority) {
      switch (priority) {
        case 'High':
          return 'error';
        case 'Medium':
          return 'warning';
        case 'Low':
          return 'success';
        default:
          return 'grey';
      }
    },

    getCategoryColor(category) {
      const colors = {
        'Work': 'indigo',
        'Personal': 'teal',
        'Health': 'green',
        'Finance': 'blue',
        'Study': 'purple'
      };
      return colors[category] || 'grey';
    },

    editTask(task) {
      this.editedIndex = this.tasks.findIndex(t => t.id === task.id);
      this.editedItem = Object.assign({}, task);
      this.dialog = true;
    },

    deleteTask(id) {
      const index = this.tasks.findIndex(t => t.id === id);
      if (confirm('Are you sure you want to delete this task?')) {
        this.tasks.splice(index, 1);
      }
    },

    closeDialog() {
      this.dialog = false;
      setTimeout(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      }, 300);
    },

    saveTask() {
      if (!this.editedItem.title || !this.editedItem.category) {
        alert('Please fill in all required fields');
        return;
      }

      if (this.editedIndex > -1) {
        Object.assign(this.tasks[this.editedIndex], this.editedItem);
      } else {
        this.editedItem.id = Date.now().toString();
        this.tasks.push(this.editedItem);
      }

      this.closeDialog();
    }
  }
};
</script>

<style scoped>
.search-field >>> .v-input__slot {
  min-height: 40px !important;
}

.task-card {
  transition: all 0.3s ease;
  border-left: 4px solid transparent;
}

.task-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 12px rgba(0, 0, 0, 0.1) !important;
}

.completed-task {
  opacity: 0.7;
  border-left-color: #4CAF50;
}

.on-hover {
  transform: translateY(-5px);
  transition: transform 0.3s ease;
}

.celebration-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 9999;
}

@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.05);
  }
  100% {
    transform: scale(1);
  }
}

.pulse-btn {
  animation: pulse 1.5s infinite;
}
</style>