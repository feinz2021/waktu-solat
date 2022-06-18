<template>
  <body>
    <div class="container">
      <vue3-simple-typeahead
        id="typeahead_id"
        placeholder="Start writing..."
        :items="this.senaraiDaerah"
        :minInputLength="1"
        @selectItem="selectItemEventHandler"
        @onFocus="onFocusEventHandler"
      >
      </vue3-simple-typeahead>

      <table>
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
            <td></td>
          </tr>
        </tbody>
      </table>

      <!-- <ul>
        <li
          v-for="(nama, index) in senaraiDaerah"
          :key="nama"
          @click="clicked(index)"
          style="cursor: pointer"
        >
          {{ index + 1 + "." }}
          {{ nama }}
        </li>
      </ul> -->
    </div>
  </body>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      byState: [],
      nama: "",
      waktu: "",
      negeri: "sabah",
      senaraiDaerah: [],

      temp: "",
    };
  },
  mounted() {
    window.M.AutoInit();
    axios
      .get(
        "https://waktu-solat-api.herokuapp.com/api/v1/prayer_times.json?negeri=" +
          this.negeri
      )
      .then((response) => (this.byState = response.data.data));
  },
  methods: {
    onFocusEventHandler() {
      try {
        const numberOfDaerah = this.byState.zon.length;
        for (let i = 0; i < numberOfDaerah; i++) {
          this.senaraiDaerah[i] = this.byState.zon[i].nama;
        }
      } catch (e) {
        console.log("a");
      }
    },
    // selectItemEventHandler(a) {
    //   const result = this.taskList.find(({ taskName }) => taskName === a);
    // },
    clicked(i) {
      this.temp = this.byState.zon[i].waktu_solat[1].time;
    },
  },
};
</script>

<style>
</style>