<template>
  <div
    class="mFiltersBox fade-in-home"
    :style="{
      height: `${splash ? '100vh' : 'auto'}`,
    }"
  >
    <!-- splash -->
    <div v-if="splash" class="canvasBoxBabylon">
      <div class="splashText">FrozenShots</div>
    </div>

    <div v-if="!splash" class="mFilters fade-in-home">
      <div class="mImgBackgroundOverlay"></div>
      <div v-if="menu" class="mBackgroundOverlay fade-in fade-out"></div>
      <div class="mSelectFilters">
        <div class="mSelectBtn" @click="datePicker = !datePicker">
          <div class="">select date</div>
        </div>
        <div v-if="dateSelected" class="mSelectedValue">
          <div class="">{{ dateSelected }}</div>
        </div>
        <div class="mSelectBtn" @click="spotPickerToggle = !spotPickerToggle">
          <div class="">select spot</div>
        </div>
        <div v-if="spotSelected" class="mSelectedValue">
          <div class="">{{ spotSelected }}</div>
        </div>
        <div
          class="mSelectBtn"
          @click="timeSlotPickerToggle = !timeSlotPickerToggle"
        >
          <div class="">select time-slot</div>
        </div>
        <div v-if="timeSlotSelected" class="mSelectedValue">
          <div class="">{{ timeSlotSelected }}</div>
        </div>
        <!-- <div
          v-if="dateSelected && spotSelected && timeSlotSelected"
          class="findShotsBtn"
          @click="getShots()"
        >
          <div class="findShots">find your shots</div>
        </div> -->
        <div class="findShotsBtn" @click="getShots()">
          <div class="findShots">find your shots</div>
        </div>
      </div>
      <div class="fotoBox">
        <v-img
          v-for="(foto, i) in filteredFotos"
          :key="`foto_${i}`"
          @click="selectFoto()"
          :src="foto"
          class="grey lighten-2 fotoImg"
          :aspect-ratio="16 / 9"
        >
          <template v-slot:placeholder>
            <v-row class="fill-height ma-0" align="center" justify="center">
              <v-progress-circular
                indeterminate
                color="grey lighten-5"
              ></v-progress-circular>
            </v-row>
          </template>
        </v-img>
      </div>
    </div>

    <!-- ######## overlays ######## -->

    <!-- date -->
    <v-overlay class="overlayZIndex" :value="datePicker"
      ><div @click="datePicker = !datePicker">
        <i class="fas fa-times closeMonthPicker"></i>
      </div>
      <v-date-picker v-model="dateSelected"></v-date-picker>
      <div class="monthBtn">
        <v-btn
          type="submit"
          color="primary"
          rounded
          dark
          depressed
          @click="selectMonth(dateSelected)"
        >
          SELECT
        </v-btn>
      </div>
    </v-overlay>
    <!-- spot -->
    <v-overlay class="overlayZIndex" :value="spotPickerToggle"
      ><div @click="spotPickerToggle = !spotPickerToggle">
        <i class="fas fa-times closeMonthPicker"></i>
      </div>
      <div class="spotCard">
        <div
          class="spot"
          v-for="(spot, i) in spots"
          :key="`spot_${i}`"
          @click="selectSpot(spot.slug)"
        >
          <div class="spotName">
            {{ spot.name }}
          </div>
          <v-img
            v-if="spot && spot.img"
            :src="spot.img"
            class="grey lighten-2 spotImg"
            :aspect-ratio="16 / 9"
          >
            <template v-slot:placeholder>
              <v-row class="fill-height ma-0" align="center" justify="center">
                <v-progress-circular
                  indeterminate
                  color="grey lighten-5"
                ></v-progress-circular>
              </v-row>
            </template>
          </v-img>
        </div>
      </div>
    </v-overlay>
    <!-- time slot -->
    <v-overlay class="overlayZIndex" :value="timeSlotPickerToggle"
      ><div @click="timeSlotPickerToggle = !timeSlotPickerToggle">
        <i class="fas fa-times closeMonthPicker"></i>
      </div>
      <div class="slotCard">
        <div
          class="slot"
          v-for="(slot, i) in timeSlots"
          :key="`slot_${i}`"
          @click="selectSlot(slot)"
        >
          <div class="slotName">
            {{ slot }}
          </div>
        </div>
      </div>
    </v-overlay>
  </div>
</template>
<script>
import { mapState, mapGetters } from "vuex";
import { db } from "../../main";
import firebase from "firebase";

export default {
  data() {
    return {
      splash: true,
      dateSelected: null,
      spotSelected: null,
      timeSlotSelected: null,
      datePicker: false,
      spotPickerToggle: false,
      timeSlotPickerToggle: false,
      spots: null,
      timeSlots: [
        "8",
        "9",
        "10",
        "11",
        "12",
        "13",
        "14",
        "15",
        "16",
        "17",
        "18",
      ],
      filteredFotos: [],
      selectedFotos: null,
    };
  },
  created() {
    console.log(window.innerWidth);
    this.$store.commit("isMetamorphosis", true);
    this.setSplash();
    this.$store.commit("selectEl", "mHome");
    setTimeout(() => {
      this.$store.commit("toggleHomeMenuColor", true);
    }, 4000);
    this.getSpots();
  },
  methods: {
    async getShots() {
      var storageRef = firebase.storage().ref();
      // Create a reference under which you want to list
      var listRef = storageRef.child(
        `${this.dateSelected}_${this.spotSelected}_${this.timeSlotSelected}`
      );

      // Find all the prefixes and items.
      listRef
        .listAll()
        .then((res) => {
          console.log(res);
          res.prefixes.forEach((folderRef) => {
            // All the prefixes under listRef.
            // You may call listAll() recursively on them.
          });
          res.items.forEach((itemRef) => {
            // All the items under listRef.
            itemRef.getDownloadURL().then((url) => {
              // Do something with the URL ...
              console.log(url);
              this.filteredFotos.push(url);
            });
          });
        })
        .catch(function (error) {
          // Uh-oh, an error occurred!
        });
    },
    async getSpots() {
      db.collection("spots")
        .get()
        .then((querySnapshot) => {
          const documents = querySnapshot.docs.map((doc) => doc.data());
          // do something with documents
          console.log(documents);
          this.spots = documents;
        });
    },
    selectSlot(slot) {
      this.timeSlotPickerToggle = !this.timeSlotPickerToggle;
      this.timeSlotSelected = slot;
      console.log(slot);
    },
    selectSpot(spot) {
      this.spotPickerToggle = !this.spotPickerToggle;
      this.spotSelected = spot;
      console.log(spot);
    },
    selectMonth(date) {
      this.datePicker = !this.datePicker;
      console.log(date);
    },
    setSplash() {
      setTimeout(() => {
        this.splash = false;
      }, 3000);
    },
  },
  computed: {
    ...mapState({
      isLoggedIn: "isLoggedIn",
      userRole: "userRole",
      globalMessage: "globalMessage",
      isMetamorphosis: "isMetamorphosis",
      menu: "menu",
    }),
  },
};
</script>
<style lang="scss">
.mFiltersBox {
  width: 100%;
  padding-top: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
  position: relative;
  z-index: 8000;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: center;
  background-size: cover;
  background-color: #0076ff;
}
.mFilters {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  z-index: 9996;
}
.spotCard {
  width: 60vw;
  height: 400px;
  background: white;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 30px;
  .spot {
    color: rgb(66, 66, 66);
    width: 40%;
    height: 90%;
    padding: 10px;
    margin: 10px;
    border-radius: 4px;
    background: rgb(224, 224, 224);
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    transition: 1s;
    cursor: pointer;

    &:hover {
      box-shadow: 0px 0px 5px 4px rgba(189, 189, 189, 1);
      transition: 1s;
    }
    .spotImg {
      border-radius: 3px;
      width: 90%;
    }
  }
}
.fotoBox {
  width: 95vw;
  margin-top: 100px;
  height: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  .fotoImg {
    width: 20%;
    border-radius: 4px;
    margin: 10px;
  }
}
.slotCard {
  width: 60vw;
  height: 400px;
  background: white;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-wrap: wrap;
  padding: 30px;
  .slot {
    color: rgb(66, 66, 66);
    width: 20%;
    height: 20%;
    padding: 10px;
    margin: 10px;
    border-radius: 4px;
    background: rgb(224, 224, 224);
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    transition: 1s;
    cursor: pointer;

    &:hover {
      box-shadow: 0px 0px 5px 4px rgba(189, 189, 189, 1);
      transition: 1s;
    }
  }
}
.overlayZIndex {
  z-index: 9999 !important;
}
.closeMonthPicker {
  font-size: 25px;
  margin: 20px;
  cursor: pointer;
}
.monthBtn {
  display: flex;
  justify-content: center;
  padding: 5px;
}
.splashText {
  color: white;
  font-size: 30px;
}
.canvasBoxBabylon {
  height: 60vh;
  width: 60vw;
  display: flex;
  justify-content: center;
  align-items: center;
  canvas {
    outline: none !important;
  }
}

.mSelectFilters {
  display: flex;
  width: 100vw;
  z-index: 9996;
  flex-direction: column;
  justify-content: flex-start;
  align-items: center;
  .mSelectBtn {
    color: white !important;
    text-decoration: none !important;
    font-weight: bold;
    font-size: 45px;
    cursor: pointer;
    border: 0.5px solid white;
    border-radius: 150px;
    width: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100px;
    transition: 1s;
    margin: 20px 0;

    &:hover {
      background: grey;
      transition: 1s;
    }
  }
  .mSelectedValue {
    color: white !important;
    text-decoration: none !important;
    font-weight: bold;
    font-size: 35px;
    border: 0.5px solid white;
    border-radius: 150px;
    width: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 50px;
    transition: 1s;
    margin: 10px 0;
    &:hover {
      // background: grey;
      // transition: 1s;
    }
  }
  .findShotsBtn {
    color: white !important;
    text-decoration: none !important;
    font-weight: bold;
    font-size: 35px;
    border: 0.5px solid white;
    border-radius: 150px;
    width: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 50px;
    transition: 1s;
    margin: 10px 0;
    background: rgba(214, 214, 214, 0.767);
    transition: 1s;

    cursor: pointer;
    &:hover {
      background: grey;
      transition: 1s;
    }
  }
}
.findShots {
  font-size: 35px;
}
.hide {
  display: none !important;
}
.fade-out {
  -webkit-animation: fade-out 1s ease-out both;
  animation: fade-out 1s ease-out both;
}
.fade-in {
  -webkit-animation: fade-in 1.2s cubic-bezier(0.39, 0.575, 0.565, 1) both;
  animation: fade-in 1.2s cubic-bezier(0.39, 0.575, 0.565, 1) both;
}

// ##
@media (max-width: 1300px) {
}

// ##
@media (max-width: 1050px) {
  .mTripartition {
    display: none;
  }
  .mSelectFilters {
    .mSelectBtn {
      font-size: 30px !important;
      width: 60% !important;
    }
  }
}
@media (max-width: 800px) {
  .spotCard {
    width: 90vw !important;
    padding: 5px;
  }
}
@media (max-width: 600px) {
  .canvasBoxBabylon {
    height: 100vh;
    width: 100vw;
  }
  .splashText {
    left: calc(50vw - 125px);
  }
  .mSelectFilters {
    .mSelectBtn {
      font-size: 21px !important;
      width: 80% !important;
    }
  }
  .mSelectedValue {
    font-size: 20px !important;
  }
  .findShots {
    font-size: 20px !important;
  }
}
</style>