<template>
  <div>
    <div class="notification is-success has-text-centered">
      <h1 class="title">SafeTown</h1>
      <p>
        Sistema de predicción de crimenes para Bucaramanga, Colombia.
      </p>
    </div>
    <div class="container">
      <div class="box">
        <form @submit.prevent="predict()">
          <div class="level">
            <div class="level-item">
              <b-field label="Barrio">
                <b-select 
                  v-model="neighborhood"
                  placeholder="Seleccione..."
                  required>
                  <option
                    v-for="(v, i) in neighborhoodsList"
                    :key="i"
                    :value="i">
                    {{ v }}
                  </option>
                </b-select>
              </b-field>
            </div>
            <div class="level-item">
              <b-field label="Día de la semana">
                <b-select
                  v-model="day"
                  placeholder="Seleccione..."
                  required>
                  <option
                    v-for="(v, i) in sortedDays"
                    :key="i"
                    :value="i">
                    {{ v }}
                  </option>
                </b-select>
              </b-field>
            </div>
            <div class="level-item">
              <b-field label="Estado civil">
                <b-select
                  v-model="status"
                  placeholder="Seleccione..."
                  required>
                  <option
                    v-for="(v, i) in statusList"
                    :key="i"
                    :value="i">
                    {{ v }}
                  </option>
                </b-select>
              </b-field>
            </div>
            <div class="level-item">
              <b-field label="Curso de vida">
                <b-select
                  v-model="lifetime"
                  placeholder="Seleccione..."
                  required>
                  <option
                    v-for="(v, i) in lifetimeList"
                    :key="i"
                    :value="i">
                    {{ v }}
                  </option>
                </b-select>
              </b-field>
            </div>
            <div class="level-item">
              <button
                class="button is-success"
                type="submit">
                Predecir
              </button>
            </div>
          </div>
        </form>

        <br>

        <div class="level">
          <div class="level-item">
            <b-taglist attached>
              <b-tag
                type="is-dark"
                size="is-large">
                Tipo de conducta
              </b-tag>
              <b-tag
                type="is-danger"
                size="is-large">
                {{ conduct }}
              </b-tag>
            </b-taglist>
          </div>
        </div>

        <hr>
        
        <div class="box">
          <div class="map-container">
            <l-map :zoom="zoom" :center="center">
              <l-tile-layer
                url="https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png" />
              <l-marker :lat-lng="center" />
            </l-map>
          </div>
        </div>

        <br><br><br>

      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import { LMap, LTileLayer, LMarker } from 'vue2-leaflet';

export default {
  name: 'SafeTown',
  components: {
    LMap,
    LTileLayer,
    LMarker,
  },
  data () {
    return {
      neighborhoodsList: {
        1: 'Barrancabermeja',
        2: 'Cabecera del Llano',
        3: 'Centro',
        4: 'Corregimiento 1',
        5: 'Corregimiento 2',
        6: 'Corregimiento 3',
        7: 'Floridablanca',
        8: 'García Rovira',
        9: 'Giron',
        10: 'La Ciudadela',
        11: 'La Concordia',
        12: 'La Pedregosa',
        13: 'Lagos del Cacique',
        14: 'Medellin',
        15: 'Morrorico',
        16: 'Mutis',
        17: 'Norte',
        18: 'Nor Oriental',
        19: 'Ocidental',
        20: 'Oriental',
        21: 'Piedecuesta',
        22: 'Provenza',
        23: 'San Francisco',
        24: 'Sin informacion',
        25: 'Sur',
        26: 'Sur Occidente'
      },
      daysList: {
        1: 'Viernes',
        2: 'Sabado',
        3: 'Domingo',
        4: 'Lunes',
        5: 'Martes',
        6: 'Miercoles',
        7: 'Jueves'
      },
      statusList: { 
        1: 'CASADO',
        2: 'DIVORCIADO',
        3: 'NO REPORTA',
        4: 'SEPARADO',
        5: 'SOLTERO',
        6: 'UNION LIBRE',
        7: 'VIUDO'
      },
      lifetimeList: {
        1: 'Adolescencia',
        2: 'Adultez',
        3: 'Infancia',
        4: 'Jovenes',
        5: 'NO REPORTA',
        6: 'Persona Mayor',
        7: 'Primera infancia',
      },
      neighborhood: null,
      day: null,
      status: null,
      lifetime: null,
      zoom: 16,
      center: [7.12539, -73.1198], // Las coordenadas de Bucaramanga
      conduct: null
    }
  },
  computed: {
    sortedDays() {
      const order = [3, 4, 5, 6, 7, 1, 2]
      return order.map(day => this.daysList[day])
    }
  },
  methods: {
    async predict () {
      try {
        var data = {
            'dia_semana': this.day,
            'curso_de_vida': parseInt(this.lifetime),
            'barrios_hecho': parseInt(this.neighborhood),
            'estado_civil_persona': parseInt(this.status)
        } 
        await axios.post('https://zest-gravel-tarn.glitch.me/predict', data).then((response) => {
            var data = response.data
            this.conduct = data.conduct
            this.center = [data.lat, data.lng]
        })
      } catch (error) {
        console.log(error);
      }
    }
  }
}
</script>

<style>
.map-container {
  width: 100%;
  height: calc(100vh - 100px);
}
</style>
