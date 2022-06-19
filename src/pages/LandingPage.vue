<template>
  <body>
    <div class="container">

      <vue3-simple-typeahead
        id="typeahead_id2"
        placeholder="Taip Negeri . . ."
        :items="this.negeri"
        :minInputLength="1"
        @selectItem="selectNegeri"
      >
      </vue3-simple-typeahead>
       <label for="typeahead_id2">taip disini⬆️</label>

      <vue3-simple-typeahead
        id="typeahead_id"
        placeholder="Taip Bandar/Daerah . . ."
        :items="this.senaraiDaerah"
        :minInputLength="1"
        @selectItem="selectDaerah"
        @onFocus="onFocusEventHandler"
      >
      </vue3-simple-typeahead>
       <label for="typeahead_id">taip disini⬆️</label>

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
  </body>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      byCity: [],
      cityIndex: 0,
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
  },
  methods: {
    onFocusEventHandler() {
      try {
          const numberOfDaerah = this.byCity.length;
          for (let i = 0; i < numberOfDaerah; i++) {
            this.senaraiDaerah[i] = this.byCity[i].nama;
          }
      } catch (e) {
        this.$toast.open({
          message: "Error Loading Data, Please Try Again Later . . .",
          type: "error",
          duration: 3000,
          dismissible: true,
          position: "bottom",
        });
        console.log(e);
      }
    },
    selectDaerah(a) {
      const result = this.byCity.findIndex(({ nama }) => nama === a);
      this.cityIndex = result;
      this.waktuLength = this.byCity[result].waktu_solat.length;

      console.log(a);

      this.temp2 = result;
      this.temp = this.byCity[result].waktu_solat[0].name;

      for (let i = 0; i < this.waktuLength; i++) {
        this.waktuSolat[i] = this.byCity[result].waktu_solat[i].time;
      }
    },
    selectNegeri(a) {
      this.senaraiDaerah = [];
      return axios
        .get(
          "https://waktu-solat-api.herokuapp.com/api/v1/prayer_times.json?negeri=" +
            a
        )
        .then((response) => (this.byCity = response.data.data.zon));
    },
  },
};
</script>

<style>
</style>