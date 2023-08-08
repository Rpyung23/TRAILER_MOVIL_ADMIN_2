<template>
  <base-nav
    container-classes="container-fluid"
    class="navbar-top border-bottom navbar-expand"
    :class="{ 'bg-success navbar-dark': type === 'default' }"
  >
    <div id="titleGestionInteligente" class="col barraTitle">
      <span class="titleNavbar text-white mb-0 font-weight-bold"
        >TRAILER MOVIL ADMINISTRADOR
      </span>
    </div>
    <!-- Navbar links -->
    <ul class="navbar-nav align-items-center ml-md-auto">
      <li class="nav-item d-xl-none">
        <!-- Sidenav toggler -->
        <div
          class="pr-3 sidenav-toggler"
          :class="{
            active: $sidebar.showSidebar,
            'sidenav-toggler-dark': type === 'default',
            'sidenav-toggler-light': type === 'light',
          }"
          @click="toggleSidebar"
        >
          <div class="sidenav-toggler-inner">
            <i class="sidenav-toggler-line"></i>
            <i class="sidenav-toggler-line"></i>
            <i class="sidenav-toggler-line"></i>
          </div>
        </div>
      </li>

      <iframe
        id="reloj"
        src="https://free.timeanddate.com/clock/i8cxjbwb/n4622/fn8/fs18/fcfff/tc2a8d62/pa6/th1/ta1"
        frameborder="0"
        width="142"
        height="32"
      ></iframe>

      <!--<span class="reloj">{{ hora }}</span>-->
      <div class="espacio" style="margin-right: 0.8rem"></div>

      <base-button
        size="sm"
        type="secondary"
        v-if="notificationPermiso"
        @click="showAjustes()"
        style="margin-right: 0rem; color: #172b4d"
      >
        <span class="btn-inner--icon"
          ><i class="ni ni-notification-70" style="font-size: 1rem"></i
        ></span>
      </base-button>
    </ul>

    <ul class="navbar-nav align-items-center ml-auto ml-md-0">
      <base-dropdown
        menu-on-right
        class="nav-item"
        tag="li"
        title-tag="a"
        title-classes="nav-link pr-0"
      >
        <a href="#" class="nav-link pr-0" @click.prevent slot="title-container">
          <div class="media align-items-center">
            <span
              class="avatar avatar-sm rounded-circle"
              style="background-color: white"
            >
              <img alt="Image placeholder" :src="logo" />
            </span>
            <div class="media-body ml-2 d-none d-lg-block">
              <span class="mb-0 text-sm font-weight-bold"></span>
            </div>
          </div>
        </a>

        <template>
          <li class="dropdown-item">
            <i class="ni ni-circle-08"></i>
            <span>{{ nameUsuario }}</span>
          </li>
          <div class="dropdown-divider"></div>
          <a :href="hrefLogOut" class="dropdown-item" @click="cerrarSession()">
            <i class="ni ni-user-run"></i>
            <span>Cerrar Sesión</span>
          </a>
        </template>
      </base-dropdown>
    </ul>
  </base-nav>
</template>
<script>
import {
  Table,
  TableColumn,
  Select,
  Option,
  Autocomplete,
  DatePicker,
  RadioButton,
  Radio,
  Notification,
  Checkbox,
  CheckboxButton,
  CheckboxGroup,
  Popover,
  Button,
  Loading,
  Switch,
  MessageBox,
} from "element-ui";

import { CollapseTransition } from "vue2-transitions";
import { Modal, BaseAlert } from "@/components/argon-core";
import BaseNav from "@/components/argon-core/Navbar/BaseNav.vue";
import { Badge } from "element-ui";
import jwt_decode from "jwt-decode";

export default {
  components: {
    CollapseTransition,
    BaseNav,
    Modal,
    [MessageBox.name]: MessageBox,
    BaseAlert,
    [Badge.name]: Badge,
    [Switch.name]: Switch,
    [DatePicker.name]: DatePicker,
    [Select.name]: Select,
    [Option.name]: Option,
    [Table.name]: Table,
    [Notification.name]: Notification,
    [Autocomplete.name]: Autocomplete,
    [TableColumn.name]: TableColumn,
    [RadioButton.name]: RadioButton,
    [Radio.name]: Radio,
    [Checkbox.name]: Checkbox,
    [CheckboxButton.name]: CheckboxButton,
    [CheckboxGroup.name]: CheckboxGroup,
    [Popover.name]: Popover,
    [Button.name]: Button,
  },
  props: {
    type: {
      type: String,
      default: "default", // default|light
      description:
        "Look of the dashboard navbar. Default (Green) or light (gray)",
    },
  },
  computed: {
    routeName() {
      const { name } = this.$route;
      return this.capitalizeFirstLetter(name);
    },
  },
  data() {
    return {
      ModalClasicAjustes: false,
      hora: "00:00:00",
      nameUsuario: "ADMINISTRADOR",
      mListaAlertasDispositivosNotificaciones: [],
      mListaAlertasDispositivosNotificacionesAux: [],
      logo: "../img/brand/logo_login.jpg",
      activeNotifications: false,
      showMenu: false,
      searchModalVisible: false,
      visibleBadgeNotification: false,
      hrefLogOut: "./",
      searchQuery: "",
      permisos: null,
      oEspacio: false,
      nameEmpresa: "TRAILER MOVIL ADMINISTRADOR",
      mListNotifications: [],
      token: this.$cookies.get("token"),
      notificationPermiso:false
    };
  },
  methods: {
    showAjustes() {
      this.readNotificationAll()
      this.ModalClasicAjustes = true;
    },
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
    },
    toggleNotificationDropDown() {
      this.activeNotifications = !this.activeNotifications;
    },
    cerrarSession() {
      this.$router.push({ path: "./login", replace: true });
    },
    closeDropDown() {
      this.activeNotifications = false;
    },
    toggleSidebar() {
      this.$sidebar.displaySidebar(!this.$sidebar.showSidebar);
    },
    hideSidebar() {
      this.$sidebar.displaySidebar(false);
    },
  },
  mounted() {
    this.hrefLogOut = "./login";
  },
};
</script>
<style>
@font-face {
  font-family: "digital-7";
  src: url("assets/css/nucleo/fonts/digital-7.ttf") format("truetype");
}

.container-list-Notificaiones-Alerta {
  height: 20rem !important;
  overflow: auto;
}

.barraTitle {
  display: flex;
  justify-content: center;
  align-content: center;
}

.titleNavbar {
  font-weight: 700;
  font-size: 1.45rem;
}

#t1 {
  color: white !important;
  /*background-color: #172b4d;*/
  background-color: #2a8d62 !important;
  width: 8.5rem !important;
  font-size: 1.5rem !important;
  font-family: "digital-7" !important;
  text-align: center !important;
  border-radius: 10px !important;
}

@media only screen and (max-width: 70em) {
  #titleGestionInteligente {
    display: none;
  }
}
</style>
