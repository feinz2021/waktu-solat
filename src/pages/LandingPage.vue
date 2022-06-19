<template>
  <body>
    <div class="container">
      <h5 class="center">Waktu Solat Malaysia</h5>

      <div v-if="!isLoading" class="row">
        <div class="col s12 m4 l4 push-m2 push-l2">
          <vue3-simple-typeahead
            id="typeahead_id2"
            placeholder="1. Taip Negeri"
            :items="this.negeri"
            :minInputLength="1"
            @selectItem="selectNegeri"
          >
          </vue3-simple-typeahead>
          <label for="typeahead_id2">1️⃣Taip Negeri</label>
        </div>
        <div class="col s12 m4 m4 push-m2 push-l2">
          <vue3-simple-typeahead
            id="typeahead_id"
            placeholder="2. Taip Bandar/Daerah"
            :items="this.senaraiDaerah"
            :minInputLength="1"
            @selectItem="selectDaerah"
          >
          </vue3-simple-typeahead>
          <label for="typeahead_id">2️⃣Taip Bandar/Daerah</label>
        </div>
      </div>

      <div v-if="isLoading" class="center">Data Loading . . .⏳</div>

      <div v-if="!isLoading" class="row">
        <div class="col s12 m8 l8 push-m2 push-l2">
          <table class="responsive-table centered">
            <thead>
              <tr>
                <th>Imsak</th>
                <th>Subuh</th>
                <th>Syuruk</th>
                <th>Zohor</th>
                <th>Asar</th>
                <th>Maghrib</th>
                <th>Isyak</th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td v-for="time in waktuSolat" :key="time">{{ time }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </body>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      stateList: [],
      stateIndex: 0,
      waktuLength: 0,
      waktuSolat: [],
      negeri: [
        "wilayah persekutuan",
        "terengganu",
        "sarawak",
        "selangor",
        "sabah",
        "perak",
        "pulau pinang",
        "perlis",
        "pahang",
        "negeri sembilan",
        "melaka",
        "kelantan",
        "kedah",
        "johor",
      ],
      senaraiDaerah: [],

      isLoading: false,

      temp: "",
      temp2: "",
    };
  },
  mounted() {
    window.M.AutoInit();
    this.isLoading = true;
    return axios
      .get("https://waktu-solat-api.herokuapp.com/api/v1/prayer_times.json")
      .then((response) => (this.stateList = response.data.data.negeri))
      .then(() => {
        this.isLoading = false;
      });
  },
  methods: {
    selectDaerah(a) {
      const cityIndex = this.stateList[this.stateIndex].zon.findIndex(
        ({ nama }) => nama === a
      );

      this.waktuLength =
        this.stateList[this.stateIndex].zon[cityIndex].waktu_solat.length;

      for (let i = 0; i < this.waktuLength; i++) {
        this.waktuSolat[i] =
          this.stateList[this.stateIndex].zon[cityIndex].waktu_solat[i].time;
      }
    },
    selectNegeri(a) {
      const result = this.stateList.findIndex(({ nama }) => nama === a);

      this.stateIndex = result;

      const numberOfDaerah = this.stateList[this.stateIndex].zon.length;
      for (let i = 0; i < numberOfDaerah; i++) {
        this.senaraiDaerah[i] = this.stateList[this.stateIndex].zon[i].nama;
      }
    },
  },
};
</script>

<style>
</style>