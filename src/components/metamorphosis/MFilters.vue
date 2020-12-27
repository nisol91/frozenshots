<template>
  <div
    class="mFiltersBox fade-in-home"
    :style="{
      height: `${splash ? '100vh' : '850px'}`,
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
          <div class="">find your shots</div>
        </div> -->
        <div class="findShotsBtn" @click="getShots()">
          <div class="">find your shots</div>
        </div>
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
      var listRef = storageRef.child("2020_12_04_snowpark112_14");

      // Find all the prefixes and items.
      listRef
        .listAll()
        .then(function (res) {
          console.log(res);
          res.prefixes.forEach(function (folderRef) {
            // All the prefixes under listRef.
            // You may call listAll() recursively on them.
          });
          res.items.forEach(function (itemRef) {
            // All the items under listRef.
            console.log(itemRef.getDownloadURL());
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

.menuEl {
  transition: 2s;
  margin: 0 10px;
  position: relative;

  &:hover {
    text-decoration: none;
    transition: 2s;
    .menuLine {
      opacity: 1;
      transition: 0.5s;
    }
  }

  .menuLine {
    position: absolute;
    top: 10px;
    opacity: 0;
    transition: 2s;
    width: 100%;
    height: 2px;
    background: white;
  }
  .menuLineShow {
    opacity: 0.7;
    transition: 0.5s;
  }
}
.mTripartition {
  position: absolute;
  top: 0px;
  width: 100vw;
  height: 100vh;
  z-index: 9996;
  display: flex;
  justify-content: center;
  align-items: center;
  .mTrip {
    display: flex;
    justify-content: center;
    align-items: flex-end;
    padding-bottom: 100px;
  }
  .mTripCenter {
    display: flex;
    justify-content: flex-end;
    align-items: center;
    padding-bottom: 0px;
    flex-direction: column;
    transition: 1s;
  }
  .mTripSx {
    transition: 1s;
  }
  .mTripDx {
    transition: 1s;
  }
  .mTripCenterHover {
    background: white;
    transition: 1s;
    width: 60vw !important;
  }
  .pushingAbout {
    width: 100vw !important;
    transition: 1s;
    background: white;
    color: black;
    .mTripTextCenter {
      color: black;
    }
  }

  .mTripCenterHidden,
  .mTripCenterHidden_2 {
    background: rgba(61, 61, 61, 0.897);
  }
  .mTripHidden {
    background: rgba(0, 0, 0, 0.89);
  }
  .mTripDxHover,
  .mTripSxHover {
    background: rgba(128, 128, 128, 0.404);
    transition: 1s;
    width: 55vw !important;
  }
  .pushingBlog,
  .pushingContents {
    width: 100vw !important;
    transition: 1s;
  }
  .pushingBlogOthers,
  .pushingContentsOthers {
    opacity: 0;
    width: 10px !important;
    transition: 1s;
  }
  .mTripText {
    color: white;
    font-weight: bold;
    font-size: 45px;
    cursor: pointer;
    width: 100%;
    display: flex;
    justify-content: space-around;
    align-items: center;
    z-index: 9995;
  }
  .mLineSx {
    margin-right: 30px;
    transition: 1s;
  }
  .mLineDx {
    margin-left: 30px;
    transition: 1s;
  }
  .mTripTextSx {
    justify-content: flex-start;
    transition: 2s;
    &:hover {
      .mLineSx {
        width: 300px;
        transition: 2s;
      }
    }
  }
  .mTripTextDx {
    justify-content: flex-end;
    transition: 2s;

    &:hover {
      .mLineDx {
        width: 300px;
        transition: 2s;
      }
    }
  }

  .mTripTextCenter {
    padding-bottom: 60px;
    transition: 2s;
    &:hover {
      color: black;
      transition: 2s;
    }
  }
  .mLineBottom {
    width: 1px;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 40px;
    border-left: 0.5px solid white;
    border-right: 0.5px solid white;
  }
  .m1 {
    width: 33%;
    height: 100%;
    border-right: 0.3px solid rgba(255, 255, 255, 0.507);
  }

  .m2 {
    width: 33%;
    height: 100%;
    border-right: 0.3px solid rgba(255, 255, 255, 0.507);
  }

  .m3 {
    width: 33%;
    height: 100%;
  }
  .mShort {
    width: 28%;
  }
}

.mLine {
  width: 50px;
  height: 1px;
  border-bottom: 2px solid white;
}

.splash {
  color: white;
  font-weight: bold;
  font-size: 30px;
}
.pulsate-fwd {
  -webkit-animation: pulsate-fwd 1.5s ease-in-out infinite both;
  animation: pulsate-fwd 1.5s ease-in-out infinite both;
}

.fade-in-home {
  -webkit-animation: fade-in 2s cubic-bezier(0.39, 0.575, 0.565, 1) both;
  animation: fade-in 2s cubic-bezier(0.39, 0.575, 0.565, 1) both;
}

.mSelectFilters {
  display: flex;
  position: absolute;
  top: 100px;
  width: 100vw;
  height: 800px;
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
}
</style>