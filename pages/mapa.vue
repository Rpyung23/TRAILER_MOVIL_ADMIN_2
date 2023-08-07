<template>
  <!-- eslint-disable vue/no-use-v-if-with-v-for,vue/no-confusing-v-for-v-if -->
  <div id="rezizeArea">
    <GmapMap
      :center="oCenter"
      :zoom="oZoom"
      :map-type-id="radioMapaMonitoreo"
      class="mapa"
      :options="{
        zoomControl: false,
        scaleControl: false,
        mapTypeControl: false,
        streetViewControl: false,
        rotateControl: false,
        fullscreenControl: false,
        disableDefaultUi: true,
      }"
    >
      <GmapMarker
        :position="{
          lat: oCenter.lat,
          lng: oCenter.lng,
        }"
        :clickable="true"
        :draggable="true"
        :optimized="true"
        @dragend="updateMarker($event.latLng)"
      />
    </GmapMap>

    <div class="containerGeocoder">
      {{ GeocoderUbicacion }}
    </div>
  </div>
</template>
<style>
.containerGeocoder {
  position: absolute;
  margin-top: 1.5rem;
  margin-right: 1.5rem;
  top: 0;
  right: 0;
  padding: 0.25rem;
  background-color: #172b4d;
  border-radius: 1rem;
  border-style: solid;
  border-color: transparent;
  color: white;
}

.containerUnidadTitulo {
  display: flex;
  width: 13rem !important;
  justify-content: space-between;
  align-content: center;
  align-items: center;
}

.RadioTiposMapaMonitoreo {
  display: flex;
  flex-direction: column;
  width: 15.5rem;
}

.is-bordered {
  margin-left: 0px !important;
  width: 15.5rem;
}

.ListadoUnidades::-webkit-scrollbar {
  display: none;
}

.itemsRuta::-webkit-scrollbar {
  display: none;
}

.min-max {
  display: flex;
  width: 100%;
  justify-content: flex-end;
  margin-right: 1.5rem;
  margin-bottom: 00.5rem;
  color: #48494b8f;
}

.bx-min-max:hover {
  cursor: pointer;
  color: #172b4d;
}

.cardRuta {
  width: 15.5rem;
  border-radius: 0.5rem;
  border-style: solid;
  border-width: 1px;
  border-color: black;
  display: flex;
  margin-bottom: 0.5rem;
  cursor: pointer;
}

.checkboxRuta {
  flex-basis: calc(10%);
  display: flex;
  justify-content: center;
  align-items: center;
}

.DetalleRuta {
  flex-basis: calc(90%);
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  display: flex;
  flex-direction: column;
}

.titleRuta {
  display: flex;
  justify-content: space-between;
}

.titleControl {
  display: flex;
  justify-content: space-between;
  padding-right: 0.5rem;
  padding-left: 0.5rem;
}

.spanTitle {
  font-size: 0.8rem;
  font-weight: 600;
  color: black;
}

.spanHoras {
  font-size: 0.77rem;
  color: black;
}

.footerRuta {
  display: flex;
  flex-direction: column;
  line-height: 0.85rem;
}

.btn-iconos {
  display: flex;
  align-items: center;
  align-content: center;
}

.dispositivo {
  color: rgba(56, 54, 54, 0.76);
  font-size: 00.7rem;
  font-family: Arial, Helvetica, sans-serif;
}

.iconosEventos {
  display: flex;
  justify-content: center;
  align-items: center;
}

.itemContainerMonitoreo {
  display: flex;

  align-items: center;
  justify-items: center;
}

.inputSearchTexto {
  border-width: 0rem;
  outline: none;
  border-color: transparent;
  caret-color: #172b4d;
}

.searchInput {
  height: 2.5rem !important;
  min-height: 2.5rem !important;
  width: 15.5rem;
  background-color: white;
  margin-left: 1rem;
  margin-bottom: 0.7rem;
  border-radius: 1rem;
  margin-left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid rgb(117, 111, 111);
}

.ListadoUnidades {
  height: 100%;
  width: calc(100%);
  margin-left: 0.5rem;
  overflow: scroll;
  display: grid;
  grid-template-columns: repeat(auto-fit, 16rem);
  justify-content: center;
}

.itemMonitoreoUnidad {
  border-width: 1px;
  border-style: solid;
  border-color: black;
  color: black;
  height: 6rem;
  font-size: 0.78rem;
  line-height: 1.05;
  width: 15.5rem;
  margin-bottom: 0.5rem;
  display: flex;
  flex-direction: column;
  justify-content: center;
  cursor: pointer;
}

.imagenitem {
  margin-right: 0.2rem;
  margin-left: 0.2rem;
}

#rezizeArea {
  width: 100%;
  overflow: auto;
  position: relative;
}

.bx {
  font-size: 1.2rem;
}

.mapa {
  width: 100%;
  height: calc(100vh - 3.8rem);
}

.tabOptionsMonitoreo {
  height: 3.3rem;
  width: 11rem;
  margin-right: 1rem;
  margin-top: 0.55rem;
  position: absolute;
  top: 0;
  right: 0;
  background-color: white;
  border-radius: 5rem;
  display: flex;
  padding: 0.4rem;
}

.itemOptionMonitoreo {
  color: white;
  flex-basis: calc(33.33%);
  display: flex;
  justify-content: center;
  align-items: center;
  border-radius: 4rem;
  color: #212529c4;
  font-size: 1.7rem;
  cursor: pointer;
}

.itemOptionMonitoreoActive {
  background-color: #172b4d;
  color: whitesmoke;
}

.paddingLabel {
  margin-bottom: 2.9rem;
  font-weight: bold;
}

.paddingLabelControl {
  margin-bottom: 2.9rem;
  font-weight: bold;
  border-color: #f71313;
  border-width: 1px;
  background-color: white;
  padding-right: 0.5rem;
  padding-left: 0.5rem;
  border-radius: 1rem;
}

#resizerXY {
  height: 97%;
  width: 0.4rem;
  /*background-color: #32325d;*/
  position: absolute;
  left: 0;
}

.container_otrosPaneles {
  position: absolute;
  height: calc(100vh - 9em);
  min-width: 17rem;
  max-width: 17rem;
  right: 0px;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  top: 0px;
  margin-top: 4.5rem;
  margin-right: 1rem;
  background-color: white;
  display: flex;
  flex-direction: column;
  /*align-items: center;*/
  align-items: center;
  border-radius: 0.5rem;
}

.itemsRuta {
  overflow: auto;
}

.container_unidades_monitoreo {
  position: absolute;
  height: calc(100vh - 9em);
  min-width: 17rem !important;
  max-width: 85vw !important;
  right: 0px;
  padding-top: 0.5rem;
  padding-bottom: 0.5rem;
  top: 0px;
  margin-top: 4.5rem;
  margin-right: 1rem;
  background-color: white;
  display: flex;
  flex-direction: column;
  /*align-items: center;*/
  justify-content: center;
  align-items: center;
  border-radius: 0.5rem;
}

.strongLetrasInfoWindows {
  font-weight: bold;
  color: black;
}

.bx-search {
  color: #172b4d;
  font-size: 1.5rem;
}

.detalleIconos {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-right: 0.2rem;
  margin-left: 0.2rem;
}
</style>

<script>
import BaseCheckbox from "@/components/argon-core/Inputs/BaseCheckbox";
import { Radio, RadioButton, Select, Option, Checkbox } from "element-ui";
export default {
  layout: "DashboardLayout",
  components: {
    BaseCheckbox,
    [Radio.name]: Radio,
    [RadioButton.name]: RadioButton,
    [Select.name]: Select,
    [Option.name]: Option,
    [Checkbox.name]: Checkbox,
  },
  data() {
    return {
      oCenter: { lat: -1.249546, lng: -78.585376 },
      oZoom: 7,
      fullscreenControl: false,
      GeocoderUbicacion: "",
    };
  },
  methods: {
    readMapa() {
      let config = {
        method: "get",
        maxBodyLength: Infinity,
        url: process.env.baseUrl + "/mapa.php",
        headers: {},
      };

      this.$axios
        .request(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
          if (response.data.status == 200) {
            this.oCenter = {
              lat: parseFloat(response.data.datos[0].latitud),
              lng: parseFloat(response.data.datos[0].longitud),
            };
            this.oZoom = 19;
            this.GeocoderUbicacion = response.data.datos[0].geocoder;
          }
        })
        .catch((error) => {
          console.log(error);
        });
    },
    async updateMarker(event) {
      console.log(event);
      this.oCenter = { lat: event.lat(), lng: event.lng() };

      var dir = await this.$axios.get(
        "https://maps.googleapis.com/maps/api/geocode/json?latlng=" +
          event.lat() +
          "," +
          event.lng() +
          "&key=" +
          process.env.mapaCredencial
      );

      var result = dir.data.results;
      this.GeocoderUbicacion =
        result.length > 0 ? result[0].formatted_address : "SIN NOMBRES";
      this.updateApi()  
    },
    updateApi() {

      let data = JSON.stringify({
        id_ubicacion: 1,
        latitud: this.oCenter.lat,
        longitud: this.oCenter.lng,
        geocoder: this.GeocoderUbicacion,
      });

      let config = {
        method: "put",
        maxBodyLength: Infinity,
        url: process.env.baseUrl + "/mapa.php",
        headers: {
          "Content-Type": "application/json",
        },
        data: data,
      };

      this.$axios
        .request(config)
        .then((response) => {
          console.log(JSON.stringify(response.data));
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
  mounted() {
    this.readMapa();
  },
};
</script>
