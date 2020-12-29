<template>
  <div
    class="mFiltersBox fade-in-home"
    :style="{
      height: `${splash ? '100vh' : 'auto'}`,
    }"
  >
    <!-- cart -->
    <div class="fCart">
      <div class="fCartNumber">
        {{ selectedFotos.length }}
      </div>
      <v-icon class="fCartIcon">mdi-cart-outline</v-icon>
    </div>
    <!-- splash -->
    <div v-if="splash" class="canvasBoxBabylon">
      <div class="splashText">FrozenShots</div>
    </div>

    <div v-if="!splash" class="mFilters fade-in-home">
      <div class="mImgBackgroundOverlay"></div>
      <div v-if="menu" class="mBackgroundOverlay fade-in fade-out"></div>
      <div class="mSelectFilters">
        <!-- input for keyboard events -->
        <GlobalEvents
          @keyup.escape="escapeCloseOverlay"
          @keyup.arrow-left="prevFotoGallery"
          @keyup.arrow-right="nextFotoGallery"
        />

        <div
          class="mSelectBtn relative-position"
          @click="datePicker = !datePicker"
          v-ripple="{ early: true, color: 'blue-grey-9' }"
        >
          <div class="">select date</div>
        </div>
        <div v-if="dateSelected" class="mSelectedValue">
          <div class="">{{ dateSelected }}</div>
        </div>
        <div
          class="mSelectBtn relative-position"
          @click="spotPickerToggle = !spotPickerToggle"
          v-ripple="{ early: true, color: 'blue-grey-9' }"
        >
          <div class="">select spot</div>
        </div>
        <div v-if="spotSelected" class="mSelectedValue">
          <div class="">{{ spotSelected }}</div>
        </div>
        <div
          class="mSelectBtn relative-position"
          @click="timeSlotPickerToggle = !timeSlotPickerToggle"
          v-ripple="{ early: true, color: 'blue-grey-9' }"
        >
          <div class="">select time-slot</div>
        </div>
        <div v-if="timeSlotSelected" class="mSelectedValue">
          <div class="">{{ timeSlotSelected }}</div>
        </div>
        <!-- <div
          v-if="dateSelected && spotSelected && timeSlotSelected"
          class="findShotsBtn relative-position"
          @click="getShots()" v-ripple="{ early: true, color: 'blue-grey-9' }"
        >
          <div class="findShots">find your shots</div>
        </div> -->
        <div
          class="findShotsBtn relative-position"
          @click="getShots()"
          v-ripple="{ early: true, color: 'blue-grey-9' }"
        >
          <div class="findShots">find your shots</div>
        </div>
      </div>
      <div class="fotoBox">
        <v-img
          v-for="(foto, i) in filteredFotos"
          :key="`foto_${i}`"
          :src="foto.src"
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
          <div class="">
            <v-icon
              v-if="foto.id"
              @click="fotoGalleryController(foto.id)"
              class="openIconMiniature"
              >mdi-image-size-select-large</v-icon
            >
          </div>
          <div class="">
            <v-icon
              v-if="foto && !selectedFotos.includes(parseInt(foto.id))"
              @click="selectFoto(foto.id)"
              class="selectIconMiniature"
              >mdi-check-circle-outline</v-icon
            >
          </div>
          <div class="">
            <v-icon
              v-if="foto && selectedFotos.includes(parseInt(foto.id))"
              @click="deSelectFoto(foto.id)"
              class="selectIconMiniature"
              >mdi-check-circle</v-icon
            >
          </div>
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
      <carousel
        class="spotCard"
        :perPageCustom="[
          [500, 1],
          [800, 2],
          [1024, 3],
        ]"
        :navigationNextLabel="'▶'"
        :navigationPrevLabel="'◀'"
        :paginationSize="30"
        :paginationPadding="15"
        :paginationActiveColor="'#0F4F99'"
      >
        <slide class="" v-for="(spot, i) in spots" :key="`spot_${i}`">
          <div class="spot" @click="selectSpot(spot.slug)">
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
                    color="black lighten-5"
                  ></v-progress-circular>
                </v-row>
              </template>
            </v-img>
          </div>
        </slide>
      </carousel>
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
    <!-- foto gallery -->
    <v-overlay class="overlayZIndex" :value="fotoGalleryToggle">
      <div class="imgGalleryCard">
        <div @click="fotoGalleryToggle = false">
          <i class="fas fa-times closePicker"></i>
        </div>
        <v-icon
          v-if="
            fotoGalleryOpenedSrc &&
            !selectedFotos.includes(parseInt(fotoGalleryOpenedSrc.id))
          "
          @click="selectFoto(fotoGalleryOpenedSrc.id)"
          class="selectIcon"
          >mdi-check-circle-outline</v-icon
        >
        <v-icon
          v-if="
            fotoGalleryOpenedSrc &&
            selectedFotos.includes(parseInt(fotoGalleryOpenedSrc.id))
          "
          @click="deSelectFoto(fotoGalleryOpenedSrc.id)"
          class="selectIcon"
          >mdi-check-circle</v-icon
        >

        <div
          class="imgGallery"
          v-if="fotoGalleryOpenedSrc"
          v-touch-swipe.left="nextFotoGallery"
        >
          <div class="imgName">
            {{ fotoGalleryOpenedSrc.id }}
          </div>
          <v-img
            v-if="fotoGalleryOpenedSrc.src"
            :src="fotoGalleryOpenedSrc.src"
            class="grey lighten-2 imgGalleryImg"
            :aspect-ratio="16 / 9"
            v-touch-swipe.right="prevFotoGallery"
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
          <div class="imgArrows">
            <v-icon class="imgArrow" @click="prevFotoGallery"
              >mdi-arrow-left</v-icon
            >

            <v-icon class="imgArrow" @click="nextFotoGallery"
              >mdi-arrow-right</v-icon
            >
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
import GlobalEvents from "vue-global-events";
import VueCarousel from "vue-carousel";
import { Carousel, Slide } from "vue-carousel";

export default {
  components: {
    Carousel,
    Slide,
    GlobalEvents,
  },
  data() {
    return {
      splash: true,
      dateSelected: "2020-12-04",
      spotSelected: "snowpark112",
      timeSlotSelected: "14",
      datePicker: false,
      spotPickerToggle: false,
      timeSlotPickerToggle: false,
      fotoGalleryToggle: false,
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
      selectedFotos: [],
      fotoGalleryOpenedSrc: null,
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
    escapeCloseOverlay() {
      if (this.datePicker) {
        this.datePicker = false;
      }
      if (this.spotPickerToggle) {
        this.spotPickerToggle = false;
      }
      if (this.timeSlotPickerToggle) {
        this.timeSlotPickerToggle = false;
      }
      if (this.fotoGalleryToggle) {
        this.fotoGalleryToggle = false;
      }
    },
    fotoGalleryController(id) {
      this.fotoGalleryToggle = true;
      for (const img of this.filteredFotos) {
        if (img.id == id) {
          var temp = new Object();
          temp["id"] = parseInt(img.id);
          temp["src"] = img.src;
          this.fotoGalleryOpenedSrc = temp;
        }
      }
    },
    selectFoto(id) {
      console.log(id);
      this.selectedFotos.push(parseInt(id));
    },
    deSelectFoto(id) {
      console.log(id);
      for (const [i, el] of this.selectedFotos.entries()) {
        if (parseInt(el) === parseInt(id)) {
          this.selectedFotos.splice(i, 1);
        }
      }
    },
    nextFotoGallery() {
      console.log(this.fotoGalleryOpenedSrc.id + 1);
      this.fotoGalleryController(this.fotoGalleryOpenedSrc.id + 1);
    },
    prevFotoGallery() {
      console.log(this.fotoGalleryOpenedSrc.id + 1);
      this.fotoGalleryController(this.fotoGalleryOpenedSrc.id - 1);
    },
    inputKeyFocus(ref) {
      document.getElementById(ref).focus();
    },
    async getShots() {
      this.filteredFotos = [];
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
              itemRef
                .getMetadata()
                .then((res) => res.name)
                .then((id) => {
                  console.log(url);
                  // removed file extension from file name (the file name is only the foto id)
                  var trimmedId = id.substring(0, id.length - 4);
                  this.filteredFotos.push({
                    id: trimmedId,
                    src: url,
                  });
                });
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
.fCart {
  width: 50px;
  position: fixed;
  top: 120px;
  right: 20px;
  display: flex;
  justify-content: space-around;
  align-items: center;
  z-index: 9999;
  border-radius: 4px;
  padding: 3px;
  cursor: pointer !important;

  .fCartNumber,
  .fCartIcon {
    color: rgb(78, 82, 92) !important;
  }
  &:hover {
    background: rgba(109, 109, 109, 0.493);
  }
}
.inputKeyControls {
  opacity: 0;
  width: 5px;
  cursor: default;
}
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
  width: 80vw;
  background: white;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 30px 0;

  .spot {
    color: rgb(66, 66, 66);
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

.imgGalleryCard {
  position: relative;
  width: 90vw;
  height: 80vh;
  margin-top: 120px;
  background: white;
  border-radius: 10px;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 3px;
  .imgGallery {
    color: rgb(66, 66, 66);
    width: 120vh;
    padding: 2px;
    margin: 2px;
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    transition: 1s;

    .imgGalleryImg {
      margin-top: 5px;
      border-radius: 3px;
      width: 90%;
      transition: 1s;

      // &:hover {
      //   box-shadow: 0px 0px 5px 4px rgba(189, 189, 189, 1);
      //   transition: 1s;
      // }
    }
    .imgName {
      font-size: 10px;
      text-align: center;
    }
    .imgArrows {
      width: 200px;
      display: flex;
      justify-content: space-around;
      align-items: center;
      .imgArrow {
        color: black !important;
        margin-top: 10px;
        cursor: pointer;
      }
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
  position: relative;
  z-index: 9998 !important;

  .fotoImg {
    width: 20%;
    border-radius: 4px;
    margin: 10px;
    cursor: pointer;
    z-index: 9998 !important;
  }
  .selectIconMiniature {
    width: 40px;
    height: 40px;
    font-size: 25px;
    cursor: pointer;
    z-index: 9999 !important;
    color: black !important;
    position: absolute !important;
    top: 10px;
    right: 10px;
    font-size: 30px !important;
    margin: 0 !important;
  }
  .openIconMiniature {
    width: 40px;
    height: 40px;
    font-size: 25px;
    cursor: pointer;
    z-index: 9999 !important;
    color: black !important;
    position: absolute !important;
    top: 10px;
    left: 10px;
    font-size: 30px !important;
    margin: 0 !important;
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
  z-index: 9999 !important;
}
.closePicker {
  font-size: 25px;
  cursor: pointer;
  z-index: 9999 !important;
  color: black;
  position: absolute;
  top: 10px;
  left: 10px;
}
.selectIcon {
  font-size: 25px;
  cursor: pointer;
  z-index: 9999 !important;
  color: black !important;
  position: absolute !important;
  top: 10px;
  right: 10px;
  font-size: 30px !important;
  margin: 0 !important;
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
  padding-top: 50px;
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
      background: rgba(155, 155, 155, 0.37);
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
    width: 80% !important;
  }
  .findShotsBtn {
    width: 80% !important;
  }
  .findShots {
    font-size: 18px !important;
  }
}
</style>