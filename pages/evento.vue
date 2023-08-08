<template>
  <div class="content">
    <base-header>
      <div class="align-items-center py-3">
        <card
          class="no-border-card col"
          style="margin-bottom: 0.5rem"
          body-classes="px-0 pb-1 card-bodyTopOpcionesRPagosVehiculoPRoduccionPanelDespachoBusqueda cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          footer-classes="pb-2"
        >
          <div
            class="cardTextoRPagosVehiculoProduccionPanelDespachoBusqueda"
          ></div>

          <div
            class="cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          >
            <div class="buttonCenterEndDerecha">
              <base-button
                icon
                type="primary"
                size="sm"
                @click="readEventoAll()"
              >
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
                ></span>
              </base-button>
              <base-button
                type="default"
                size="sm"
                @click="showModalAddEvento()"
                title="NUEVO USUARIO"
              >
                <span class="btn-inner--icon"
                  ><i class="ni ni-fat-add"></i
                ></span>
              </base-button>
            </div>
          </div>
        </card>

        <card
          class="no-border-card"
          style="margin-bottom: 0rem"
          body-classes="card-bodyRPagosVehiculoProduccion px-0 pb-1"
          footer-classes="pb-2"
        >
          <el-table
            element-loading-text="Cargando Datos..."
            :data="mListEventos"
            row-key="id"
            v-loading="loadingEventos"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 8.90rem)"
            style="width: 100%"
          >

          

            <el-table-column width="100">
              <template slot-scope="scope">
                <!--<base-button
                  size="sm"
                  title="EDITAR"
                  @click="showModalUpdateInfoUsuario(scope.row)"
                  type="primary"
                  ><i class="ni ni-ruler-pencil"></i
                ></base-button>-->

                <base-button
                  size="sm"
                  v-if="scope.row.numBoletosDisponibles > 0"
                  title="ELIMINAR"
                  @click="deleteEvento(scope.row.id_evento)"
                  type="danger"
                  ><i class="ni ni-fat-remove"></i
                ></base-button>

                <badge v-if="scope.row.numBoletosDisponibles <= 0" type="danger" class="mr-2">AGOTADO</badge>

              </template>
            </el-table-column>

            <el-table-column prop="foto" width="120">
              <template slot-scope="scope">
                <img
                  :src="scope.row.foto"
                  alt="../static/img/default.jpg"
                  class="avatar rounded-circle mr-3"
                />
              </template>
            </el-table-column>

            <el-table-column
              v-for="column in tableColumnsEventos"
              :key="column.label"
              v-bind="column"
            >
            </el-table-column>

            <el-table-column prop="estado" label="ESTADO" width="150">
              <template slot-scope="scope">
                {{ scope.row.estado == 1 ? "ACTIVADO" : "INACTIVO" }}
              </template>
            </el-table-column>

            <div slot="empty"></div>
          </el-table>
        </card>

        <modal :show.sync="modalAddEvento" size="lg">
          <div class="container_add_evento">
            <div class="input_add_evento_img">
              <img
                id="imagenPrevisualizacionEvento"
                src="../static/img/image.jpg"
                style="max-width: 100%; height: 25rem"
                alt=""
              />

              <base-button
                for="fileInput"
                class="btn-icon"
                style="width: 100%"
                type="default"
                icon="ni ni-bag-17"
                >SUBIR FOTO</base-button
              >

              <input
                type="file"
                id="fileInput"
                class="hidden-file-input"
                accept="image/*"
                @change="changeFoto()"
              />
            </div>

            <div class="input_dato_evento">
              <base-input
                prepend-icon="ni ni-diamond"
                name="NOMBRE DEL EVENTO"
                placeholder="NOMBRE DEL EVENTO"
                v-model="nombre_evento"
                rules="required"
              >
              </base-input>

              <base-input
                prepend-icon="ni ni-world"
                name="UBCICACION DEL EVENTO"
                placeholder="UBCICACION DEL EVENTO"
                rules="required"
                v-model="ubicacion_evento"
              >
              </base-input>

              <el-date-picker
                type="datetime"
                placeholder="FECHA EVENTO"
                style="width: 100%; margin-bottom: 1.5rem"
                v-model="fecha_evento"
              >
              </el-date-picker>

              <base-input
                prepend-icon="ni ni-money-coins"
                name="PRECIO DEL EVENTO"
                placeholder="PRECIO DEL EVENTO"
                rules="required"
                v-model="precio_evento"
              >
              </base-input>

              <base-input
                prepend-icon="ni ni-tag"
                name="CANT BOLETOS"
                placeholder="CANT BOLETOS"
                rules="required"
                v-model="numBoletosDisponibles_evento"
              >
              </base-input>

              <base-input
                prepend-icon="ni ni-single-copy-04"
                name="DETALLE EVENTO"
                placeholder="DETALLE EVENTO"
                style="margin-bottom: 0rem"
                rules="required"
                v-model="detalle_evento"
              >
              </base-input>
            </div>
          </div>

          <template slot="footer">
            <base-button type="danger" class="ml-auto" @click="closeEvento()"
              >CANCELAR
            </base-button>
            <base-button type="default" @click="saveEvento()"
              >GUARDAR EVENTO</base-button
            >
          </template>
        </modal>
      </div>
    </base-header>
  </div>
</template>

<script>
import flatPicker from "vue-flatpickr-component";
import { getBase64LogoReportes } from "../util/logoReport";
import { convertSecondtoTimeString } from "../util/fechas";
import {
  validarNumeroCelular,
  validarCedula,
  validarCorreoElectronico,
} from "../util/validaciones";
import "flatpickr/dist/flatpickr.css";
import {
  getFecha_dd_mm_yyyy,
  FechaStringToHour,
  getformatFechaDateTime,
} from "../util/fechas";

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
  Tag,
} from "element-ui";

import RouteBreadCrumb from "@/components/argon-core/Breadcrumb/RouteBreadcrumb";
import { BasePagination } from "@/components/argon-core";
import clientPaginationMixin from "~/components/tables/PaginatedTables/clientPaginationMixin";
import swal from "sweetalert2";
import Tabs from "@/components/argon-core/Tabs/Tabs";
import TabPane from "@/components/argon-core/Tabs/Tab";

export default {
  mixins: [clientPaginationMixin],
  layout: "DashboardLayout",
  components: {
    Tabs,
    TabPane,
    BasePagination,
    flatPicker,
    RouteBreadCrumb,
    [Tag.name]: Tag,
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
  data() {
    return {
      mListEventos: [],
      loadingEventos: false,
      tableColumnsEventos: [
        {
          prop: "nombre",
          label: "NOMBRE EVENTO",
          minWidth: 240,
        },
        {
          prop: "ubicacion",
          label: "UBICACION",
          minWidth: 270,
        },
        {
          prop: "fecha_evento",
          label: "F. EVENTO",
          minWidth: 230,
        },
        {
          prop: "numBoletosDisponibles",
          label: "CANT BOLETO",
          minWidth: 180,
        },
        {
          prop: "precio",
          label: "PRECIO ($)",
          minWidth: 180,
        },
      ],
      modalAddEvento: false,
      img_src_evento: null,
      firebaseConfig: {
        apiKey: "AIzaSyBCq1qLjoJBKcZs9saFysslpNX2t7TT3Ak",
        authDomain: "trailer-movil.firebaseapp.com",
        projectId: "trailer-movil",
        storageBucket: "trailer-movil.appspot.com",
        messagingSenderId: "314437642188",
        appId: "1:314437642188:web:c1f8cb95607d466a661f69",
        measurementId: "G-CZLHZXV0ZH",
      },
      appFirebase: null,
      oFileFoto: null,

      nombre_evento: null,
      detalle_evento: null,
      ubicacion_evento: null,
      foto_evento: null,
      fecha_evento: null,
      precio_evento: null,
      numBoletosDisponibles_evento: null,
    };
  },
  methods: {
    async readEventoAll() {
      this.loadingEventos = true;
      this.mListEventos = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/evento.php?estado=active"
        );

        if (datos.data.status == 200) {
          /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
          this.mListEventos.push(...datos.data.datos);
        } else if (datos.data.status == 300) {
          Notification.info({
            title: "Panel Salidas",
            message: "No existen datos disponibles.",
          });
        } else {
          Notification.error({
            title: "Panel Salidas",
            message: datos.data.msm,
          });
        }
      } catch (error) {
        console.log(error);
        Notification.info({
          title: "TryCatch Panel Salidas",
          message: error.toString(),
        });
      }
      this.loadingEventos = false;
    },
    showModalAddEvento() {
      /*document.getElementById("imagenPrevisualizacionEvento").src =
        "./static/img/image.jpg";*/
      this.modalAddEvento = true;
    },
    uploadImage() {
      this.foto_evento = null;
      var self = this;

      if (this.oFileFoto != null) {
        // Crea una referencia al almacenamiento de Firebase con el nombre de archivo que deseas
        var storageRef = firebase.storage().ref(this.oFileFoto.name);

        // Sube el archivo a Firebase Storage
        var uploadTask = storageRef.put(this.oFileFoto);

        // Monitorea el progreso de la carga (opcional)
        uploadTask.on(
          "state_changed",
          function (snapshot) {
            // Aquí puedes obtener información sobre el progreso de la carga si lo deseas
            var progress =
              (snapshot.bytesTransferred / snapshot.totalBytes) * 100;
            console.log("Progreso de carga: " + progress + "%");
          },
          function (error) {
            // Manejo de errores si ocurre alguno durante la carga
            console.error("Error al cargar la foto:", error);
          },
          function () {
            // Si la carga se completa exitosamente, puedes obtener la URL de la imagen
            uploadTask.snapshot.ref
              .getDownloadURL()
              .then(function (downloadURL) {
                self.foto_evento = downloadURL;
                console.log("URL de la foto subida:", downloadURL);
              });
          }
        );
      } else {
        console.log("FILE ES NULL");
      }
    },
    changeFoto() {
      console.log("CHANGUE FOTO");
      var fileInput = document.getElementById("fileInput");
      // Los archivos seleccionados, pueden ser muchos o uno
      const archivos = fileInput.files;
      // Si no hay archivos salimos de la función y quitamos la imagen
      if (!archivos || !archivos.length) {
        //$imagenPrevisualizacionUpdate.src = "";
        //console.log("SIN ARCHICOS SELECT")
        return;
      }
      // Ahora tomamos el primer archivo, el cual vamos a previsualizar
      this.oFileFoto = archivos[0];
      // Lo convertimos a un objeto de tipo objectURL
      const objectURL = URL.createObjectURL(this.oFileFoto);
      // Y a la fuente de la imagen le ponemos el objectURL
      this.img_src_evento = objectURL;
      document.getElementById("imagenPrevisualizacionEvento").src = objectURL;
      this.uploadImage();
    },
    saveEvento() {
      try {
        if (
          this.nombre_evento == null &&
          this.detalle_evento == null &&
          this.ubicacion_evento == null &&
          this.fecha_evento == null &&
          this.precio_evento == null &&
          this.numBoletosDisponibles_evento == null
        ) {
          this.$notify({
            title: "Evento",
            message: "NO SE PERMITEN DATOS VACIOS",
            type:"danger"
          });
          return
        }

        let data = JSON.stringify({
          nombre: this.nombre_evento,
          detalle: this.detalle_evento,
          ubicacion: this.ubicacion_evento,
          foto: this.foto_evento,
          fecha_evento: getformatFechaDateTime(this.fecha_evento),
          precio: this.precio_evento == null ? 500 : this.precio_evento,
          numBoletosDisponibles:
            this.numBoletosDisponibles_evento == null
              ? 0.0
              : this.numBoletosDisponibles_evento,
        });

        console.log(data);

        let config = {
          method: "post",
          maxBodyLength: Infinity,
          url: process.env.baseUrl + "/evento.php",
          headers: {
            "Content-Type": "application/json",
          },
          data: data,
        };

        this.$axios
          .request(config)
          .then((response) => {
            console.log(JSON.stringify(response.data));
            if (response.data.status == 200) {
              this.readEventoAll();
              this.closeEvento()
            } else {
              alert("NO SE REGISTRO EL EVENTO");
            }
          })
          .catch((error) => {
            console.log(error);
            alert(error.toString());
          });
      } catch (error) {
        alert(error.toString());
      }
    },
    closeEvento() {
      this.nombre_evento = null;
      this.detalle_evento = null;
      this.ubicacion_evento = null;
      this.foto_evento = null;
      this.fecha_evento = null;
      this.precio_evento = null;
      this.numBoletosDisponibles_evento = null;
      this.modalAddEvento = false;
    },
    deleteEvento(id_evento) {
      try {
        let data = JSON.stringify({
          id_evento: id_evento,
        });

        let config = {
          method: "delete",
          maxBodyLength: Infinity,
          url: process.env.baseUrl + "/evento.php",
          headers: {
            "Content-Type": "application/json",
          },
          data: data,
        };

        this.$axios
          .request(config)
          .then((response) => {
            if (response.data.status == 200) {
              this.readEventoAll();
            } else {
              alert("NO SE PUDO ELIMINAR EL EVENTO");
            }
          })
          .catch((error) => {
            alert(error.toString());
            console.log(error);
          });
      } catch (error) {
        alert(error.toString());
      }
    },
  },
  mounted() {
    this.appFirebase = firebase.initializeApp(this.firebaseConfig);

    this.readEventoAll();
  },
};
</script>
<style>
.hidden-file-input {
  position: absolute;
  top: 0;
  left: 0;
  /*width: 100%;*/
  height: 100%;
  opacity: 0;
  cursor: pointer;
}

.container_add_evento {
  display: flex;
  flex-direction: row;
}

.input_add_evento_img {
  flex: 1;
}

.input_dato_evento {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  margin-left: 1rem;
}

.containerModalRecorridoPanelDespacho {
  display: flex;
}
.cardControlesMarc {
  height: calc(80vh);
  width: 18rem;
}

.form-controlPersonal {
  display: block;
  width: 100%;
  /* height: calc(1.5em + 1.25rem + 2px); */
  padding: 0.625rem 0.75rem;
  font-size: 0.875rem;
  font-weight: 400;
  line-height: 1.5;
  color: #8898aa;
  background-color: #fff;
  background-clip: padding-box;
  outline: none;
  border: 1px solid #dee2e6;
  border-radius: 0.25rem;
  margin-bottom: 0rem;
  box-shadow: 0 3px 2px rgba(233, 236, 239, 0.05);
  transition: all 0.15s cubic-bezier(0.68, -0.55, 0.265, 1.55);
}

.el-loading-text {
  color: black !important;
}

.el-icon-loading {
  color: black !important;
}

.cardTextoRPagosVehiculoProduccionPanelDespachoBusqueda {
  display: flex;
  align-items: center;
  flex-wrap: wrap;
}

.cardopcinesRPagosVehiculoProduccionPanelDespachoBusqueda {
  display: flex;
  align-items: center;
}

.cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda {
  display: flex;
  justify-content: space-between;
}

.el-table .warning-row-panelControlProduccion {
  background: rgba(255, 0, 0, 0.342) !important;
}

.el-table .success-row-panelControlProduccion {
  background: hsla(34, 88%, 61%, 0.384) !important;
}

.el-table .finalizado-row-panelControlProduccion {
  background: rgba(140, 248, 126, 0.384) !important;
}

.el-table .diferido-row-panelControlProduccion {
  background: hsla(226, 88%, 61%, 0.274) !important;
}

.no-border-card .card-footer {
  border-top: 0;
}

.card-bodyRPagosVehiculoProduccion {
  padding: 0rem !important;
  height: calc(100vh - 8.59rem);
  overflow: none;
}

.card-bodyTopOpcionesRPagosVehiculoPRoduccionPanelDespachoBusqueda {
  padding-top: 0.25rem !important;
}
.cardOpcinesRPagosVehiculoProduccionPanelDespachoBusqueda {
  display: flex;
  align-items: center;
}
</style>
