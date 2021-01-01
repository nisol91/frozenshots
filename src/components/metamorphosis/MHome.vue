<template>
  <div
    class="mHomeBox fade-in-home"
    :style="{
      background: `${splash ? 'rgb(80, 80, 80)' : '#0076ff'}`,
    }"
  >
    <!-- splash -->
    <div v-if="splash" class="canvasBoxBabylon">
      <div class="splashText">FrozenShots</div>
    </div>

    <div v-if="!splash" class="mHome fade-in-home">
      <div class="mImgBackgroundOverlay"></div>
      <div v-if="menu" class="mBackgroundOverlay fade-in fade-out"></div>
      <div class="mTripartitionMobile">
        <div
          class="mTripMobile relative-position"
          :to="{ name: 'mFilters' }"
          v-ripple="{ early: false, color: 'blue-grey-9' }"
          @click="pushSelect"
        >
          <div class="">freeze your moments</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import { mapState, mapGetters } from "vuex";

export default {
  data() {
    return {
      splash: true,
    };
  },
  created() {
    console.log(window.innerWidth);
    this.$store.commit("isMetamorphosis", true);
    this.setSplash();
    this.$store.commit("selectEl", "mHome");
    setTimeout(() => {
      this.$store.commit("toggleHomeMenuColor", false);
    }, 2000);
  },
  methods: {
    pushSelect() {
      setTimeout(() => {
        this.$router.push({
          name: "mFilters",
        });
      }, 500);
    },
    setSplash() {
      setTimeout(() => {
        this.splash = false;
      }, 4000);
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
    cameraZoom: function () {
      var w = window.innerWidth;
      if (w < 600) {
        return 15;
      } else {
        return 5;
      }
    },
  },
};
</script>
<style lang="scss">
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
.mHomeBox {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  position: relative;
  z-index: 8000;
  background-repeat: no-repeat;
  background-attachment: fixed;
  background-position: center;
  background-size: cover;
}

.mImgBackgroundOverlay {
  position: absolute;
  top: 0px;
  width: 100vw;
  height: 100vh;
  z-index: 8100;
  // background: rgba(155, 155, 155, 0.589);
}
.mBackgroundOverlay {
  position: absolute;
  top: 0px;
  width: 100vw;
  height: 100%;
  z-index: 9997;
  background: rgba(22, 22, 22, 0.589);
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
.mHome {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  z-index: 9996;
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

.mTripartitionMobile {
  display: flex;
  position: absolute;
  top: 100px;
  width: 100vw;
  height: calc(100vh - 100px);
  z-index: 9996;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  .mTripMobile {
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
  .mTripartition {
    .mTripText {
      font-size: 40px;
    }
    .mLineSx {
      margin-right: 10px;
    }
    .mLineDx {
      margin-left: 10px;
    }
  }
}
// ##
@media (max-width: 1050px) {
  .mTripartition {
    display: none;
  }
  .mTripartitionMobile {
    .mTripMobile {
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
  .mTripartitionMobile {
    .mTripMobile {
      font-size: 21px !important;
      width: 80% !important;
    }
  }
}
</style>