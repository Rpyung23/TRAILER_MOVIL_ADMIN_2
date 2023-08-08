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
          <div class="cardTextoRPagosVehiculoProduccionPanelDespachoBusqueda">
            <el-select
              v-model="mSelectEvent"
              multiple
              collapse-tags
              placeholder="EVENTO"
            >
              <el-option
                v-for="item in mListEvent"
                :key="item.id_evento"
                :label="item.nombre"
                :value="item.id_evento"
              >
              </el-option>
            </el-select>
          </div>

          <div
            class="cardSelectRubrosEstadosPagosVehiculoProduccionContainerPanelDespachoBusqueda"
          >
            <div class="buttonCenterEndDerecha">
              <base-button
                icon
                type="primary"
                size="sm"
                @click="readEventoTicket()"
              >
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
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
            :data="mListEventosTicket"
            row-key="id"
            v-loading="loadingEventos"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 8.90rem)"
            style="width: 100%"
          >
            <el-table-column width="100">
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  title="ANULAR RECIBO"
                  v-if="scope.row.is_devolucion == 1"
                  @click="deleteEvento(scope.row.id_evento)"
                  type="default"
                  ><i class="ni ni-send"></i
                ></base-button>
              </template>
            </el-table-column>

            <el-table-column
              v-for="column in tableColumnsEventos"
              :key="column.label"
              v-bind="column"
            >
            </el-table-column>

            <el-table-column prop="estado" label="ESTADO" width="320">
              <template slot-scope="scope">
                {{
                  scope.row.is_devolucion == 1
                    ? "RESERVA PAGADA - PERO SIN ASIENTOS"
                    : ""
                }}
              </template>
            </el-table-column>

            <div slot="empty"></div>
          </el-table>
        </card>
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
      mListEventosTicket: [],
      loadingEventos: false,
      tableColumnsEventos: [
        {
          prop: "nombre",
          label: "NOMBRE EVENTO",
          minWidth: 240,
        },
        {
          prop: "nombres",
          label: "COMPRADOR",
          minWidth: 240,
        },
        {
          prop: "num_recibo",
          label: "N° RECIBO",
          minWidth: 270,
        },
        {
          prop: "cantBoletos",
          label: "CANT. BOLETOS",
          minWidth: 180,
        },
        {
          prop: "totalFactura",
          label: "TOTAL $",
          minWidth: 180,
        },
      ],
      mListEvent: [],
      mSelectEvent: null,
    };
  },
  methods: {
    async readEventoTicket() {
      this.loadingEventos = true;
      this.mListEventosTicket = [];
      try {
        var datos = await this.$axios.post(
          process.env.baseUrl + "/evento_ticket.php",{
            eventos: this.mSelectEvent == null || this.mSelectEvent.length == 0 ? null : this.mSelectEvent.join(",")
          }
        );

        if (datos.data.status == 200) {
          /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
          this.mListEventosTicket.push(...datos.data.datos);
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
    async readEventos() {
      this.mListEvent = []

      let config = {
        method: "get",
        maxBodyLength: Infinity,
        url: process.env.baseUrl + "/evento.php?estado=active",
        headers: {},
      };

      this.$axios
        .request(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          if(response.data.status == 200)
          {
            this.mListEvent.push(...response.data.datos)
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    this.readEventos()
    this.readEventoTicket()
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
