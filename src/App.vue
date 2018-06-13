<template>
  <div id="app">
    <section class="section">
      <div class="columns">
        <div class="column is-half is-offset-one-quarter container">
          <div class="content">
            <h1>Géocodage en masse</h1>
            <div class="modal" :class="{'is-active': tutorielVisible}">
              <div class="modal-background"></div>
              <div class="modal-content">
                 <img src="src/assets/tuto.gif">
              </div>
              <button class="modal-close is-large" aria-label="close" @click="tutorielVisible = false"></button>
            </div>
              
              <a @click="tutorielVisible = true">Afficher le tutoriel</a>
            </p>
            <h2>Input</h2>
            <p>Collez ici votre liste d'adresses. Une adresse par ligne!</p>
            <p>
              <textarea v-model="input" class="textarea is-small" :disabled="isLoading"></textarea>
            </p>
            <p class="has-text-centered">
              <a class="button is-primary is-large" @click="geocode()">C'est parti!</a>
            </p>
            <h2>Output</h2>
            <p>Les latitudes et longitudes apparaîtront ici. Copiez-les et collez-les dans votre logiciel de tableur.</p>
            <p>
              <div class="control" :class="{'is-loading': isLoading }">
                <textarea v-model="output" class="textarea is-small"></textarea>
              </div>
            </p>
          </div>
        </div>
      </div>
    </section>
    <footer class="footer">
      <p>
        Ce service a été programmé par <a href="http://nkb.fr">Nicolas Kayser-Bril</a> et utilise le géocodeur de <a href="https://nominatim.openstreetmap.org/">Nominatim</a>.
      </p>
      <p>
        Il s'inspire du <a href="https://www.findlatitudeandlongitude.com/batch-geocode/">Batch Geocoder</a> de David B. Zwiefelhofer.
      </p>
      <p>
         <a href="https://github.com/n-kb/batch-geocode">Still ♡ Github</a>.
      </p>
    </footer>
  </div>
</template>

<script>

import axios from 'axios'

export default {
  name: 'app',
  data () {
    return {
      input : "",
      output: "",
      tutorielVisible: false,
      isLoading: false
    }
  },
  methods: {
    geocode () {
      this.isLoading = true;
      var addresses = this.input.split("\n");
      var base_url = "https://nominatim.openstreetmap.org/search/?format=json&limit=1&q=";
      var output_addresses = [["lat", "lon", "output_address"].join("\t")];
      var self = this;
      var count = 0

      var geocode_single = function() {
        var address = addresses[count]
        var lat = 0
        var lon = 0
        var output_address = ""
        axios.get(base_url + encodeURIComponent(address)).then(response => {
          console.log(response.data.length > 0)
          if (response.data.length > 0) {
            lat = response.data[0].lat
            lon = response.data[0].lon
            output_address = response.data[0].display_name
          } else {
            lat = "",
            lon = "",
            output_address = ""
          }

          output_addresses.push([lat, lon, output_address].join("\t"))
          self.output = output_addresses.join("\n")
          if (count == addresses.length - 1) {
            self.isLoading = false;
          } else {
            count++;
            geocode_single();
          }
        });
      }

      geocode_single();

    }
  }
}
</script>

<style lang="scss">
$family-primary: "Arial";
@import "~bulma/bulma"
</style>
