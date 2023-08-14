<template>
  <div>
    <!-- Header -->
    <div class="header bg-gradient-success py-7 py-lg-8 pt-lg-9">
      <div class="container">
        <div class="header-body text-center mb-7"></div>
      </div>
      <div class="separator separator-bottom separator-skew zindex-100">
        <svg
          x="0"
          y="0"
          viewBox="0 0 2560 100"
          preserveAspectRatio="none"
          version="1.1"
          xmlns="http://www.w3.org/2000/svg"
        >
          <polygon
            class="fill-default"
            points="2560 0 2560 100 0 100"
          ></polygon>
        </svg>
      </div>
    </div>
    <!-- Page content -->
    <div class="container mt--16 pb-5">
      <div class="row justify-content-center">
        <div class="col-lg-5 col-md-7">
          <div class="card bg-secondary border-0 mb-0">
            <div class="card-header bg-transparent">
              <div class="text-muted text-center">
                <small
                  ><img src="/img/brand/logo_login.jpg" class="logo-login"
                /></small>
              </div>
            </div>
            <div class="card-body">
              <div class="text-center" style="margin-bottom: 0.5rem;">
                  <strong>TRAILER MOVIL ADMINISTRADOR</strong>
                </div>
              <validation-observer
                v-slot="{ handleSubmit }"
                ref="formValidator"
              >
                <form role="form" @submit.prevent="handleSubmit(OnLogin)">
                  <base-input
                    alternative
                    class="mb-3"
                    name="User"
                    type="text"
                    :rules="{ required: true, email: false }"
                    prepend-icon="ni ni-single-02"
                    placeholder="Usuario"
                    v-model="model.email"
                  >
                  </base-input>

                  <base-input
                    alternative
                    class="mb-3"
                    name="Password"
                    
                    :rules="{ required: true, min: 4 }"
                    prepend-icon="ni ni-lock-circle-open"
                    type="password"
                    placeholder="Contraseña"
                    v-model="model.password"
                  >
                  </base-input>

                  <base-checkbox v-model="model.rememberMe"
                    >Acuérdate de mí</base-checkbox
                  >
                  <div class="text-center">
                    <base-button
                      type="primary"
                      native-type="submit"
                      class="my-4"
                      >Ingresar al sistema</base-button
                    >
                  </div>
                </form>
              </validation-observer>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style>
.mt--16 {
  margin-top: -16rem !important;
}

.logo-login {
  height: 6rem !important;
  width: 8rem !important;
}
</style>
<script>
import axios_ from "axios";
import Swal from "sweetalert2";

export default {
  layout: "AuthLayout",
  head() {
    return {
      title:"TRAILER MOVIL ADMINISTRADOR",
    };
  },
  data() {
    return {
      app: this,
      context: this,
      model: {
        email: "",
        password: "",
        rememberMe: false,
      },
    };
  },
  methods: {
    async OnLogin() 
    {
      
      if (this.model.email != "" && this.model.password != "") {

        var api =
          process.env.baseUrl+"/login.php";
          try{
            var datos = await axios_.post(api, {
              codigo:"empleado",
              user: this.model.email,
              pass: this.model.password,
        });

        var obj = datos.data

        if (obj.status == 200) 
        {
          Swal.fire({
            title: "TRAILER MOVIL ADMINISTRADOR",
            text: "Bienvenido",
            icon: "success",
            allowOutsideClick: false,
            confirmButtonText: "Aceptar",
          }).then((result) => {
            if (result.isConfirmed) 
            {
              this.$cookies.set("user_trailer_admin",obj.user)
              this.$cookies.set("nombres_trailer_admin",obj.nombres)
              //this.$cookies.set("token",obj.token)
              this.$router.push("/evento");
            }
          });
        } else {
          Swal.fire({
            title: "TRAILER MOVIL ADMINISTRADOR",
            text: "Credenciales no válidas",
            icon: "error",
            confirmButtonText: "Aceptar",
          });
        }
          }catch(e){
            console.log(e)
          }
      } else {
        alert("DATOS VACIOS");
      }
    },
  },
  mounted() {
  },
};
</script>