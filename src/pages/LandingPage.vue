<template>
  <header></header>
  <body>
    <main>
      <div class="container">
        <div style="margin-top: 50px"></div>
        <div class="row">
          <div class="col s12 m10 l10 push-m1 push-l1">
            <div
              class="card-panel"
              style="background-color: rgba(255, 255, 255, 0.6)"
            >
              <h5 class="center capital-first">Waktu Solat Malaysia</h5>

              <div v-if="!isLoading" class="row">
                <div class="col s12 m4 l4 push-m2 push-l2">
                  <vue3-simple-typeahead
                    id="typeahead_id2"
                    placeholder="Negeri"
                    :items="this.negeri"
                    :minInputLength="1"
                    @selectItem="selectNegeri"
                  >
                  </vue3-simple-typeahead>
                  <label style="font-size: large" for="typeahead_id2"
                    >1. Taip Negeri⬆️</label
                  >
                </div>
                <div class="col s12 m4 l4 push-m2 push-l2">
                  <vue3-simple-typeahead
                    id="typeahead_id"
                    placeholder="Bandar/Daerah"
                    :items="this.senaraiDaerah"
                    :minInputLength="1"
                    @selectItem="selectDaerah"
                  >
                  </vue3-simple-typeahead>
                  <label style="font-size: large" for="typeahead_id"
                    >2. Taip Bandar/Daerah⬆️</label
                  >
                </div>
              </div>

              <div v-if="isLoading" class="center">Data Loading . . .⏳</div>

              <div v-if="!isLoading">
                <div
                  class="center"
                  style="font-weight: bold; text-transform: capitalize"
                >
                  {{ negeriSave }},
                  {{ daerahSave }}
                </div>
                <div class="center" style="font-weight: bold">
                  {{ dateSave }}
                </div>
                <table class="responsive-table centered">
                  <thead>
                    <tr>
                      <th><u>Imsak</u></th>
                      <th><u>Subuh</u></th>
                      <th><u>Syuruk</u></th>
                      <th><u>Zohor</u></th>
                      <th><u>Asar</u></th>
                      <th><u>Maghrib</u></th>
                      <th><u>Isyak</u></th>
                    </tr>
                  </thead>
                  <tbody>
                    <tr>
                      <td
                        style="font-weight: bold"
                        v-for="time in waktuSolat"
                        :key="time"
                      >
                        {{ time24to12(time) }}
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>
            </div>
          </div>
        </div>
        <div style="margin-bottom: 50px"></div>
      </div>
      
    </main>
  </body>
  <footer class="page-footer" style="background-color: transparent">
    <div class="footer-copyright" style="background-color: #00796b">
      <div class="container">
        <a
          class="grey-text text-lighten-4 left"
          href="https://zaimramlan.github.io/waktu-solat-api/"
          ><u>Prayer Time API</u></a
        >
        <span class="right">
          Photo by
          <a
            href="https://unsplash.com/@fahrulazmi?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText"
            class="grey-text text-lighten-4"
            ><u>Fahrul Azmi</u></a
          >
          on
          <a
            href="https://unsplash.com/s/photos/mosque-malaysia?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText"
            class="grey-text text-lighten-4"
            ><u>Unsplash</u></a
          >
        </span>
      </div>
    </div>
  </footer>
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
      dateSave: "",
      negeriSave: "",
      daerahSave: "",
      isLoading: false,
    };
  },
  mounted() {
    document.title = "Waktu Solat Malaysia";
    window.M.AutoInit();
    const nowDate = new Date();
    this.dateSave =
      nowDate.getDate() +
      "/" +
      (nowDate.getMonth() + 1) +
      "/" +
      nowDate.getFullYear();

    this.isLoading = true;
    return axios
      .get("https://waktu-solat-api.herokuapp.com/api/v1/prayer_times.json")
      .then((response) => (this.stateList = response.data.data.negeri))
      .then(() => {
        this.isLoading = false;
      })
      .then(() => {
        try {
          const localStateIndex = localStorage.getItem("stateIndex");
          const localCityIndex = localStorage.getItem("cityIndex");
          this.negeriSave = localStorage.getItem("negeriSave");
          this.daerahSave = localStorage.getItem("daerahSave");

          for (let i = 0; i < 7; i++) {
            this.waktuSolat[i] =
              this.stateList[localStateIndex].zon[localCityIndex].waktu_solat[
                i
              ].time;
          }
        } catch {
          return 0;
        }
      });
  },
  methods: {
    selectNegeri(a) {
      this.senaraiDaerah = [];
      localStorage.setItem("negeriSave", a);

      const result = this.stateList.findIndex(({ nama }) => nama === a);
      this.stateIndex = result;

      localStorage.setItem("stateIndex", result);

      const numberOfDaerah = this.stateList[this.stateIndex].zon.length;
      for (let i = 0; i < numberOfDaerah; i++) {
        this.senaraiDaerah[i] = this.stateList[this.stateIndex].zon[i].nama;
      }
    },
    selectDaerah(a) {
      this.negeriSave = localStorage.getItem("negeriSave");
      this.daerahSave = a;
      localStorage.setItem("daerahSave", a);

      const cityIndex = this.stateList[this.stateIndex].zon.findIndex(
        ({ nama }) => nama === a
      );

      localStorage.setItem("cityIndex", cityIndex);

      this.waktuLength =
        this.stateList[this.stateIndex].zon[cityIndex].waktu_solat.length;

      for (let i = 0; i < this.waktuLength; i++) {
        this.waktuSolat[i] =
          this.stateList[this.stateIndex].zon[cityIndex].waktu_solat[i].time;
      }
    },
    time24to12(time) {
      // Check correct time format and split into components
      time = time
        .toString()
        .match(/^([01]\d|2[0-3])(:)([0-5]\d)(:[0-5]\d)?$/) || [time];

      if (time.length > 1) {
        // If time format correct
        time = time.slice(1); // Remove full string match value
        time[5] = +time[0] < 12 ? " AM" : " PM"; // Set AM/PM
        time[0] = +time[0] % 12 || 12; // Adjust hours
      }
      return time.join(""); // return adjusted time or original string
    },
  },
};
</script>

<style>
body {
  display: flex;
  min-height: 100vh;
  flex-direction: column;

  background-image: url("../assets/bg.jpg");
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-size: cover;
  background-position: center;
}

main {
  flex: 1 0 auto;
}

footer {
  position: fixed;
  right: 0;
  left: 0;
  bottom: 0;
}
</style>