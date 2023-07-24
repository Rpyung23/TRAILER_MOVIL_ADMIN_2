<template>
<div class="escudo">
  <section>
    <div class="circle"></div>
    <div class="contentDashboard">
      <div class="textBox">
        <h2>“Nada se pierde, todo se composta”<br><span> COMPOSTLAB WEB </span></h2>
        <p>Esta es una Aplicación Web desarrollada por estudiantes de la Escuela Superior Politécnica de Chimborazo     (ESPOCH) para el manejo de datos provenientes del proceso de compostaje en la planta Porlón del GAD Municipal del Cantón Riobamba.
        </p>
        <a href="#">Más Información</a>
      </div>
      <div class="imgBox">
        <img src="../static/img/res1.png" class="starbucks">
      </div>
    </div>
    <ul class="thumb">
      <li><a href="https://www.gadmriobamba.gob.ec/"><img src="../static/img/municipio.png"></a></li>
      <li><a href="https://www.espoch.edu.ec/es/"><img src="../static/img/espoch.png"></a></li>
      <li><img src="../static/img/alcadia1.png"></li>
    </ul>
  </section>
</div>
</template>


<style>
*
{
    margin: 0;
    padding: 0;
    box-sizing:  border-box;    
}

section{
  position: relative;
  width: 100%;
  min-height: 100vh;
  padding: 100px;
  padding-top: 40px;
  justify-content: space-between;
  align-items: center;

}

.contentDashboard{
    position: relative;
    width: 100%;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.contentDashboard .textBox{
  position: relative;
  max-width: 600px;
}

.contentDashboard .textBox h2 {
  color: #333;
  font-size: 3em;
  line-height: 1.4em;
  font-weight: 500;
}

.contentDashboard .textBox h2 span{
  color: #017143;
  font-size: 1.2em;
  font-weight: 900;
}

.contentDashboard .textBox p{
  color: #333;
}

.contentDashboard .textBox a{
  display: inline-block;
  margin-top: 20px;
  padding: 8px 20px;
  background: #017143;
  color: #fff;
  border-radius: 40px;
  font-weight: 500;
  letter-spacing: 1px;
  text-decoration: none;
}

.contentDashboard .imgBox{
  width: 600px;
  display: flex;
  justify-content: flex-end;
  padding-right: 50px;
  margin-top: 50px;
}

.contentDashboard .imgBox img{
  max-width: 340px;
}

.thumb{
  position: absolute;
  left: 50%;
  bottom: 20px;
  transform: translateX(-50%);
  display: flex;
}

.thumb li{
  list-style: none;
  display: inline-block;
  margin: 0 20px;
  cursor: pointer;
  transition: 0.5s;
}

.thumb li:hover{
  transform: translateY(-15px);
}

.thumb li img{
  max-width: 60px;
}


.escudo{
  display: flex;
  justify-content: center;
  align-items: center;
  align-content: center;
  height: 90vh;
  background-color:rgb(255, 255, 255);
}

.circle {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #017143;
  clip-path: circle(600px at right 750px);
}

</style>

<script>


  export default {
    layout: 'DashboardLayout',
    components: {

    },
    data() {
      return {

      };
    },
    methods: {

    },
    mounted() {
  
    }
  };
</script>
<style>

.escudo{
  display: flex;
  justify-content: center;
  align-items: center;
  align-content: center;
  height: 90vh;
  background-color:white;
}
</style>
