<template>
  <div>
    <div>
      <navbar-view/>
      <sidebar-view/>
    </div>
    <div>
      <router-view/>
    </div>
  </div>
</template>

<script>
import sidebarView from "@/components/DashboardView/SidebarView.vue";
import navbarView from "@/components/DashboardView/NavbarView.vue";

export default {
  name: 'DashboardPage',
  components: {
    sidebarView,
    navbarView
  },
}
</script>