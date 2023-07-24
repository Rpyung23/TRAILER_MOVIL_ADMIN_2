<template>
  <!-- Pum@1500123456789 -->
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
                @click="readNotificacionAll()"
              >
                <span class="btn-inner--icon"
                  ><i class="el-icon-search"></i
                ></span>
              </base-button>
              <base-button
                type="default"
                size="sm"
                @click="showmodalAddEmail()"
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
            :data="mListEmails"
            row-key="id"
            v-loading="loadingEmail"
            class="tablePanelControlProduccion"
            header-row-class-name="thead-dark"
            height="calc(100vh - 8.90rem)"
            style="width: 100%"
          >
            <el-table-column>
              <template slot-scope="scope">
                <base-button
                  size="sm"
                  title="REENVIAR"
                  @click="sendEmails(scope.row.html_content)"
                  type="default"
                  ><i class="ni ni-send"></i
                ></base-button>
              </template>
            </el-table-column>

            <el-table-column
              v-for="column in tableColumnsEmail"
              :key="column.label"
              v-bind="column"
            >
            </el-table-column>

            <el-table-column prop="html_content" label="DETALLE EMAIL" width="950">
              <template slot-scope="scope">
                <div v-html="scope.row.html_content"></div>
              </template>
            </el-table-column>

            <div slot="empty"></div>
          </el-table>
        </card>

        <modal :show.sync="modalAddEmail" size="lg">
          <base-input
            prepend-icon="ni ni-diamond"
            name="NOMBRE DEL LA NOTIFICACION"
            placeholder="NOMBRE DEL LA NOTIFICACION"
            rules="required"
            v-model="name_content_email"
          >
          </base-input>

          <quill-editor
            ref="myTextEditor"
            :options="editorOption1"
            v-model="content_email"
            @blur="onEditorBlur($event)"
            @focus="onEditorFocus($event)"
            @ready="onEditorReady($event)"
          >
          </quill-editor>

          <div class="text-right" style="margin-top: 1rem">
            <base-button type="danger" @click="closeModalEmail()"
              >Cancelar</base-button
            >
            <base-button
              type="primary"
              @click="saveNewNotification()"
              native-type="submit"
              >Guardar</base-button
            >
          </div>

        </modal>
      </div>
    </base-header>
  </div>
</template>

<script>
import Quill from "quill";
const Font = Quill.import("formats/font");
Font.whitelist = ["mirza", "roboto"];
Quill.register(Font, true);

import flatPicker from "vue-flatpickr-component";

import "flatpickr/dist/flatpickr.css";
import { getFecha_dd_mm_yyyy, FechaStringToHour } from "../util/fechas";

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
      mListEmails: [],
      loadingEmail: false,
      tableColumnsEmail: [
        {
          prop: "name_notificacion",
          label: "NOMBRE NOTIFICACION",
          minWidth: 280,
        },
        {
          prop: "cant_alcanzada",
          label: "CANT ENV.",
          minWidth: 150,
        },
        {
          prop: "ultimo_envio",
          label: "U. FECHA",
          minWidth: 180,
        },

      ],
      modalAddEmail: false,
      editorOption: {
        modules: {
          toolbar: "#toolbar",
        },
      },
      editorOption1: {
        modules: {
          toolbar: [
            [{ header: [1, 2, 3, 4, 5, 6, false] }],
            ["bold", "italic", "underline", "strike"],
            ["blockquote", "code-block"],
            [{ header: 1 }, { header: 2 }],
            [{ list: "ordered" }, { list: "bullet" }],
            [{ script: "sub" }, { script: "super" }],
            [{ indent: "-1" }, { indent: "+1" }],
            [{ direction: "rtl" }],
            [{ color: [] }, { background: [] }],
            [
              {
                font: [
                  "Arial",
                  "serif",
                  "sans-serif",
                  "Microsoft YaHei",
                  "monospace",
                  "cursive",
                ],
              },
            ],
            [{ font: ["mirza", "roboto"] }],
            //['link', 'image', 'video']
          ],
        },
        // placeholder: 'Compose an epic...'
      },
      editorOption2: {
        modules: {
          toolbar: "#toolbar-b",
        },
      },
      mListClientes: [],
      content_email: null,
      name_content_email:null
    };
  },
  methods: {
    async readNotificacionAll() {
      this.loadingEmail = true;
      this.mListEmails = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/notification.php?tipo=2"
        );

        if (datos.data.status == 200) {
          /*Notification.success({
              title: "Panel Salidas",
              message: "Datos consultados con éxito",
            });*/
          this.mListEmails.push(...datos.data.datos);
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
      this.loadingEmail = false;
    },
    showmodalAddEmail() {
      this.modalAddEmail = true;
    },
    onEditorBlur(editor) {
      console.log("editor blur!", editor);
    },
    onEditorFocus(editor) {
      console.log("editor focus!", editor);
    },
    onEditorReady(editor) {
      console.log("editor ready!", editor);
    },
    customButtonClick() {
      console.log(this);
    },
    update(event) {
      console.log("hahha", event);
      event.preventDefault();
      return false;
    },
    async readAllClientes() {
      this.mListClientes = [];
      try {
        var datos = await this.$axios.get(
          process.env.baseUrl + "/cliente.php/all_clientes"
        );
        if (datos.data.status == 200) 
        {
          for(var i = 0;i<datos.data.datos.length > 0;i++)
            {
              this.mListClientes.push(datos.data.datos[i].email);
            }
        }
      } catch (e) {
        console.log(e);
      }
    },
    async sendEmails(html_content_) {
      var datos = await this.$axios.post(
        process.env.baseUrlNodeJS + "/send_email_trailer",
        {
          subject: "TRAILER MOVIL EMAIL",
          usuarios: this.mListClientes,
          html_content: html_content_,
        }
      );

      if(datos.data.status_code == 200)
      {
        Notification.success({
            title: "CORREOS  ELECTRONICOS",
            message: "ENVIADOS CON EXITO.....!",
          });
      }else{
        Notification.error({
            title: "ERROR CORREOS  ELECTRONICOS",
            message: datos.data.msm
          });
      }
    },
    saveNewNotification()
    {
      if(this.name_content_email != null){
        if(this.content_email != null)
        {

          this.readNotificacionAll()
          console.log(this.content_email)

        }else{
          Notification.info({
            title: "ERROR CORREOS  ELECTRONICO",
            message: "PORFAVOR INGRESE EL CONTENIDO DEL CORREO"
          });
        }
      }else{
        Notification.info({
            title: "ERROR CORREOS  ELECTRONICO",
            message: "PORFAVOR INGRESE EL NOMBRE DEL CORREO"
          });
      }

      
    },
    closeModalEmail(){
      this.name_content_email = null
      this.content_email = null
      this.modalAddEmail = false
    }
  },
  computed: {
    editor() {
      return this.$refs.myTextEditor.quill;
    },
  },
  mounted() {
    this.readAllClientes();
    this.readNotificacionAll();
  },
};
</script>
<style>
.hidden-file-input {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
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
