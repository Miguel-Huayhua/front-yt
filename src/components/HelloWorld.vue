<template>
  <div class="container caja">
    <div class="rectangle l" />
    <div class="rectangle l" />
    <div class="rectangle l" />
    <div class="rectangle l" />
    <div class="rectangle l" />
    <div class="rectangle l" />

    <div class="rectangle r" />
    <div class="rectangle r" />
    <div class="rectangle r" />
    <div class="rectangle r" />
    <div class="rectangle r" />
    <div class="rectangle r" />

    <div class="row justify-content-center">
      <form
        action="https://descargaytm.herokuapp.com//descargas"
        method="post"
        class="col-9"
      >
        <div class="form-row justify-content-center">
          <div class="col-10 m-auto">
            <label class="sr-only lbl mt-4" for="inlineFormInput"
              >Introduzca la URL del video que está buscando:</label
            >
          </div>
          <div class="col-12">
            <input
              class="form-control mb-2 input mt-3"
              id="inlineFormInput"
              ref="link"
              type="text"
              name="link"
              placeholder="Introduzca link"
            />
            <button v-on:click="onSubmit" class="btn btn-sub mt-3">
              Convertir mp3
            </button>
          </div>
        </div>
      </form>
    </div>

    <div v-if="this.carga" class="container2">
      <div class="dash uno"></div>
      <div class="dash dos"></div>
      <div class="dash tres"></div>
      <div class="dash cuatro"></div>
    </div>
    <div v-if="this.carga2">
      
      <div class="descarga">
        <span>↓</span>
        <span style="--delay: 0.1s">↓</span>
        <span style="--delay: 0.3s">↓</span>
        <span style="--delay: 0.4s">↓</span>
        <span style="--delay: 0.5s">↓</span>
      </div>
    </div>
    <div class="row mt-5 main" v-if="this.mostrar">
      <div class="col-6">
        <div class="card info-back" style="">
          <img class="img" v-bind:src="this.datos.video.thumb" />

          <div class="card-body footer bg-dark">
            <h4 class="lbl info">
              Fecha Lanzamiento: {{ this.datos.video.year }}
            </h4>
            <h4 class="lbl info">
              Duración: {{ Math.floor(this.datos.video.duracion / 60) }}:{{
                this.datos.video.duracion % 60
                  ? this.duracion(this.datos.video.duracion % 60)
                  : "00"
              }}
            </h4>
            <h4 class="lbl info like">
              {{ this.datos.video.likes }}
            </h4>
            <div class="lds-heart"><div></div></div>
          </div>
        </div>
      </div>

      <div class="col-6">
        <div class="row">
          <h3 ref="titulo" class="lbl">{{ this.datos.video.nombre }}</h3>
          <h4 class="lbl">
            Duración: {{ Math.floor(this.datos.video.duracion / 60) }}:{{
              this.datos.video.duracion % 60
                ? this.duracion(this.datos.video.duracion % 60)
                : "00"
            }}
          </h4>
        </div>
        <div class="row">
          <img
            class="col-4 col-md-2 rounded-circle icono"
            v-bind:src="this.datos.autor.icono"
            alt="icono"
          />
          <div class="col-8 col-md-10 row">
            <h4 class="lbl info col-2">Autor:{{ this.datos.autor.autor }}</h4>
            <div class="col-1"></div>
            <h4 class="lbl info col-6">Subs: {{ this.datos.autor.subs }}</h4>

            <div class="col-2"></div>
          </div>
        </div>
        <div class="row justify-content-center">
          <form
            class="col-12"
            action="https://descargaytm.herokuapp.com/"
            method="post"
            v-on:submit="onSubmit2"
          >
            <input
              v-if="this.completo"
              class="btn btn-sub mt-3"
              type="submit"
              value="DESCARGAR MP3"
            />
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import "bootstrap/dist/css/bootstrap.min.css";
import "./style.css";
export default {
  name: "HelloWorld",
  data() {
    return {
      image: String,
      carga: Boolean,
      completo: Boolean,
      carga2: Boolean,
      link: String,
      datos: {
        autor: {
          autor: "",
          icono: "",
          canal: "",
          subs: "",
        },
        video: {
          thumb: "",
          nombre: "",
          year: "",
          likes: "",
          duracion: "",
          media: {},
        },
      },
      mostrar: Boolean,
    };
  },
  props: {
    msg: String,
  },
  methods: {
    onSubmit(e) {
      this.mostrar = false;
      this.carga = true;
      e.preventDefault();
      axios
        .post("https://descargaytm.herokuapp.com/descargas", {
          link: this.$refs.link.value,
        })
        .then((data) => {
          this.link = this.$refs.link.value;
          this.datos = data.data;
          this.carga = false;
          this.completo = true;
          this.mostrar = true;
        });
    },
    onSubmit2(e) {
      this.carga2 = true;
      e.preventDefault();
      axios({
        url: "https://descargaytm.herokuapp.com/",
        method: "POST",
        responseType: "blob",
        data: { link: this.link },
      }).then((response) => {
        const url = window.URL.createObjectURL(new Blob([response.data]));
        const link = document.createElement("a");
        link.href = url;
        console.log(url, response);
        link.setAttribute("download", this.$refs.titulo.textContent + ".mp3"); //or any other extension
        document.body.appendChild(link);
        link.click();
        this.carga2 = false;
      });
    },
    duracion(num) {
      if (num < 10) {
        return "0" + num;
      } else {
        return num;
      }
    },
  },
  mounted() {
    this.datos = {
      autor: {
        autor: "",
        icono: "",
        canal: "",
        subs: "",
      },
      video: {
        thumb: "",
        nombre: "",
        year: "",
        likes: "",
        duracion: "",
        media: {},
      },
    };
    this.completo = false;
    this.carga = false;
    this.carga2 = false;
    this.mostrar = false;
    console.log(this.$refs);
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>
@import url("https://fonts.googleapis.com/css2?family=Oswald:wght@200&display=swap");
body {
  background: rgb(25, 37, 82);
  background: linear-gradient(
    146deg,
    rgba(25, 37, 82, 1) 0%,
    rgba(13, 65, 63, 1) 48%,
    rgba(16, 117, 104, 1) 100%
  );
  height: 100vh;
}
.caja {
  box-shadow: 5px 5px 5px 5px rgba(136, 162, 209, 0.637);
  border-radius: 10px;
  position: relative;
  overflow: auto;
  height: 99vh;
}

.hide-form {
  display: none;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.l {
  position: absolute;
  left: 0px;
  clip-path: polygon(100% 0, 0 0, 0 100%);
  background: rgb(25, 61, 82);
}
.r {
  position: absolute;
  clip-path: polygon(100% 0, 0 0, 100% 100%) !important;
  right: 0 !important;
  background: rgb(25, 61, 82);
}
.rectangle:nth-child(1) {
  width: 20px;
  height: 20px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.829) 0%,
    rgba(117, 207, 195, 0.952) 100%
  );
}
.rectangle:nth-child(2) {
  width: 40px;
  height: 40px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.678) 0%,
    rgba(117, 207, 195, 0.705) 100%
  );
}
.rectangle:nth-child(3) {
  width: 60px;
  height: 60px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.61) 0%,
    rgba(117, 207, 195, 0.589) 100%
  );
}
.rectangle:nth-child(4) {
  width: 80px;
  height: 80px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.507) 0%,
    rgba(117, 207, 195, 0.507) 100%
  );
}
.rectangle:nth-child(5) {
  width: 100px;
  height: 100px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.479) 0%,
    rgba(117, 207, 195, 0.397) 100%
  );
}
.rectangle:nth-child(6) {
  width: 120px;
  height: 120px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.336) 0%,
    rgba(117, 207, 195, 0.253) 100%
  );
}
.rectangle:nth-child(7) {
  width: 20px;
  height: 20px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.829) 0%,
    rgba(117, 207, 195, 0.952) 100%
  );
}
.rectangle:nth-child(8) {
  width: 40px;
  height: 40px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.678) 0%,
    rgba(117, 207, 195, 0.705) 100%
  );
}
.rectangle:nth-child(9) {
  width: 60px;
  height: 60px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.61) 0%,
    rgba(117, 207, 195, 0.589) 100%
  );
}
.rectangle:nth-child(10) {
  width: 80px;
  height: 80px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.507) 0%,
    rgba(117, 207, 195, 0.507) 100%
  );
}
.rectangle:nth-child(11) {
  width: 100px;
  height: 100px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.479) 0%,
    rgba(117, 207, 195, 0.397) 100%
  );
}
.rectangle:nth-child(12) {
  width: 120px;
  height: 120px;
  background: linear-gradient(
    146deg,
    rgba(25, 61, 82, 0.336) 0%,
    rgba(117, 207, 195, 0.253) 100%
  );
}

.btn-sub {
  background: rgb(11, 190, 164);
  background: linear-gradient(
    146deg,
    rgb(32, 223, 194) 0%,
    rgb(45, 189, 211) 100%
  );
  width: 50%;
  border-radius: 40px;
  font-family: "Oswald", sans-serif;
  font-size: 20px;
  color: rgb(5, 32, 73);
}
.input {
  background: rgb(7, 42, 46) !important;
  color: white !important;
  font-family: "arial";
  border-radius: 20px;
  border: 1px solid rgba(255, 255, 255, 0.39) !important;
}
.lbl {
  color: white;
  font-family: "Oswald", sans-serif;
  font-size: 30px;
}
.descarga {
  display: flex;
  height: 50px;
  align-items: center;
  justify-content: center;
}

@keyframes arrows {
  0%,
  100% {
    color: rgb(207, 224, 214);
    transform: translateY(0);
  }
  50% {
    color: #3ab493;
    transform: translateY(20px);
  }
}

.descarga > span {
  --delay: 0s;
  font-size: 30px;
  animation: arrows 1s var(--delay) infinite ease-in;
}

.loading {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 24px * 6-4px;
  height: 20px;
  transform: translate(-50%, -50%);
}
.container2 {
  padding-top: 40px;
  display: flex;
  justify-content: center;
  margin: auto;
}

.dash {
  margin: 0 15px;
  width: 35px;
  height: 15px;
  border-radius: 8px;
  background: #5a8eac;
  box-shadow: 0 0 10px 0 #cdfff3be;
}

.uno {
  margin-right: -18px;
  transform-origin: center left;
  animation: spin 3s linear infinite;
}

.dos {
  transform-origin: center right;
  animation: spin2 3s linear infinite;
  animation-delay: 0.2s;
}

.tres {
  transform-origin: center right;
  animation: spin3 3s linear infinite;
  animation-delay: 0.3s;
}

.cuatro {
  transform-origin: center right;
  animation: spin4 3s linear infinite;
  animation-delay: 0.4s;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  25% {
    transform: rotate(360deg);
  }
  30% {
    transform: rotate(370deg);
  }
  35% {
    transform: rotate(360deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes spin2 {
  0% {
    transform: rotate(0deg);
  }
  20% {
    transform: rotate(0deg);
  }
  30% {
    transform: rotate(-180deg);
  }
  35% {
    transform: rotate(-190deg);
  }
  40% {
    transform: rotate(-180deg);
  }
  78% {
    transform: rotate(-180deg);
  }
  95% {
    transform: rotate(-360deg);
  }
  98% {
    transform: rotate(-370deg);
  }
  100% {
    transform: rotate(-360deg);
  }
}

@keyframes spin3 {
  0% {
    transform: rotate(0deg);
  }
  27% {
    transform: rotate(0deg);
  }
  40% {
    transform: rotate(180deg);
  }
  45% {
    transform: rotate(190deg);
  }
  50% {
    transform: rotate(180deg);
  }
  62% {
    transform: rotate(180deg);
  }
  75% {
    transform: rotate(360deg);
  }
  80% {
    transform: rotate(370deg);
  }
  85% {
    transform: rotate(360deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

@keyframes spin4 {
  0% {
    transform: rotate(0deg);
  }
  38% {
    transform: rotate(0deg);
  }
  60% {
    transform: rotate(-360deg);
  }
  65% {
    transform: rotate(-370deg);
  }
  75% {
    transform: rotate(-360deg);
  }
  100% {
    transform: rotate(-360deg);
  }
}
@media (max-width: 600px) {
  .caja {
    width: 100%;
  }
  .btn-sub {
    width: 100%;
    font-size: 15px;
  }
  .input {
    font-size: 10px;
  }
  .lbl {
    font-size: 18px;
  }
}
</style>
