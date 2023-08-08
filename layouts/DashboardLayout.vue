<template>
  <div class="wrapper">
    <notifications></notifications>
    <side-bar>
      <template slot-scope="props" slot="links">
        <sidebar-item
          translate="no"
          :link="{
            name: 'Evento',
            icon: 'ni ni-note-03 text-info',
            path: './evento',
          }"
        >
        </sidebar-item>

        <sidebar-item
          translate="no"
          :link="{
            name: 'Evento Ticket',
            icon: 'ni ni-single-copy-04 text-warning',
            path: './evento_ticket',
          }"
        >
        </sidebar-item>

        

        <sidebar-item
          translate="no"
          :link="{
            name: 'Mapa',
            icon: 'ni ni-pin-3 text-success',
            path: './mapa',
          }"
        >
        </sidebar-item>
        
        <sidebar-item
          translate="no"
          :link="{
            name: 'Notificaciones',
            icon: 'ni ni-notification-70 text-danger',
            path: './notificacion',
          }"
        >
        </sidebar-item>

        <sidebar-item
          translate="no"
          :link="{
            name: 'Emails',
            icon: 'ni ni-email-83 text-default',
            path: './email',
          }"
        >
        </sidebar-item>

        

      </template>
    </side-bar>
    <div class="main-content">
      <dashboard-navbar
        :type="$route.name == 'alternative' ? 'light' : 'default'"
      ></dashboard-navbar>

      <div @click="$sidebar.displaySidebar(false)">
        <nuxt></nuxt>
      </div>
    </div>
  </div>
</template>
<script>
/* eslint-disable no-new */
import PerfectScrollbar from "perfect-scrollbar";
import "perfect-scrollbar/css/perfect-scrollbar.css";

function hasElement(className) {
  return document.getElementsByClassName(className).length > 0;
}

function initScrollbar(className) {
  if (hasElement(className)) {
    new PerfectScrollbar(`.${className}`);
  } else {
    // try to init it later in case this component is loaded async
    setTimeout(() => {
      initScrollbar(className);
    }, 100);
  }
}

import DashboardNavbar from "~/components/layouts/argon/DashboardNavbar.vue";
import DashboardContent from "~/components/layouts/argon/Content.vue";
import jwt_decode from "jwt-decode";

export default {
  components: {
    DashboardNavbar,
    DashboardContent,
  },
  data() {
    return {

    };
  },
  methods: {
    initScrollbar() {
      let isWindows = navigator.platform.startsWith("Win");
      if (isWindows) {
        initScrollbar("navbar-inner");
        initScrollbar("main-content");
        initScrollbar("sidenav");
      }
    },
 
  },
  mounted() {
    this.initScrollbar()
    
  },
};
</script>
<style lang="scss"></style>
