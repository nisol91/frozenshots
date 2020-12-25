<template>
  <div
    class="mHomeBox fade-in-home"
    :style="{
      background: `${splash ? 'rgb(80, 80, 80)' : '#0076ff'}`,
    }"
  >
    <!-- splash -->
    <!-- <div v-if="splash" class="splash pulsate-fwd">METAMORPHOSI</div> -->
    <div v-if="splash" class="canvasBoxBabylon">
      <div class="splashText">FrozenShots</div>
      <!-- <Scene>
        <Property name="clearColor" color="#505050"></Property>
        <Camera
          type="arcRotate"
          :properties="{ position: $vector([cameraZoom, 0, 0]) }"
        ></Camera>
        <Entity :position="[0, 0, 0]">
          <Animation property="rotation.x" :duration="5">
            <Key frame="0%" :value="0"></Key>
            <Key frame="100%" :value="Math.PI * 2"></Key>
          </Animation>
          <Sphere
            :properties="{
              position: $vector([-0.6, -1, 0]),
            }"
            ><Material diffuse="#143b60"> </Material
          ></Sphere>
          <Sphere
            :properties="{
              position: $vector([0.6, 1, 0]),
            }"
            ><Material diffuse="#143b60"> </Material
          ></Sphere>
        </Entity>
        <Entity>
          <Animation
            property="rotation.x"
            :duration="5"
            :end="Math.PI * 2"
          ></Animation>
          <PointLight diffuse="#F0F" :position="[0, 0, 4]"></PointLight>
        </Entity>
        <Entity>
          <Animation
            property="rotation.y"
            :duration="5"
            :end="Math.PI * 2"
          ></Animation>
          <PointLight>
            <property name="diffuse" color="#0FF"></property>
            <property name="position" :vector="[0, 0, 4]"></property>
          </PointLight>
        </Entity>
        <Entity>
          <Animation
            property="rotation.z"
            :duration="5"
            :end="Math.PI * 2"
          ></Animation>
          <PointLight
            :properties="{
              diffuse: $color('#FF0'),
              specular: $color({ r: 1, g: 0, b: 0 }),
            }"
          >
            <property name="position" :vector="[0, 4, 0]"></property>
          </PointLight>
        </Entity>
      </Scene> -->
    </div>

    <div v-if="!splash" class="mHome fade-in-home">
      <div class="mImgBackgroundOverlay"></div>
      <div v-if="menu" class="mBackgroundOverlay fade-in fade-out"></div>

      <!-- desktop tripartition -->
      <!-- <div class="mTripartition">
        <div
          class="mTrip m1 mTripSx"
          :class="[
            {
              mTripSxHover: mTripSxHover,
              mTripHidden: mTripDxHover,
              mShort: mTripDxHover,
              hide: pushedAbout,
              pushingContents: pushedContents,
              pushingBlogOthers: pushedBlog,
            },
          ]"
          :style="{
            backgroundImage: `url(${mTripSxHover ? img_sx : ''})`,
            backgroundSize: `cover`,
          }"
        >
          <div
            class="mTripText mTripTextSx"
            @mouseenter="mTripSxHover = true"
            @mouseleave="mTripSxHover = false"
            @click="pushContents"
          >
            <div class="mLine mLineSx"></div>
            WE DO
          </div>
        </div>
        <div
          class="mTrip mTripCenter m2"
          :class="[
            {
              mTripCenterHover: mTripCenterHover,
              mTripCenterHidden: mTripDxHover,
              mTripCenterHidden_2: mTripSxHover,
              pushingAbout: pushedAbout,
              pushingBlogOthers: pushedBlog,
              pushingContentsOthers: pushedContents,
            },
          ]"
        >
          <div
            class="mTripText mTripTextCenter"
            @mouseenter="mTripCenterHover = true"
            @mouseleave="mTripCenterHover = false"
            @click="pushAbout"
          >
            WE ARE
          </div>
          <div class="mLineBottom"></div>
        </div>

        <div
          class="mTrip m3 mTripDx"
          :class="[
            {
              mTripDxHover: mTripDxHover,
              mTripHidden: mTripSxHover,
              mShort: mTripSxHover,
              hide: pushedAbout,
              pushingBlog: pushedBlog,
              pushingContentsOthers: pushedContents,
            },
          ]"
          :style="{
            backgroundImage: `url(${mTripDxHover ? img_dx : ''})`,
            backgroundSize: `cover`,
          }"
        >
          <div
            class="mTripText mTripTextDx"
            @mouseenter="mTripDxHover = true"
            @mouseleave="mTripDxHover = false"
            @click="pushBlog"
          >
            WE TELL
            <div class="mLine mLineDx"></div>
          </div>
        </div>
      </div> -->

      <!-- mobile tripartition -->

      <div class="mTripartitionMobile">
        <div class="mTripMobile" @click="pushAboutM">freeze your moments</div>
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
      mTripCenterHover: false,
      mTripDxHover: false,
      mTripSxHover: false,
      pushedAbout: false,
      pushedBlog: false,
      pushedContents: false,

      backgrounds_sx: [
        "https://endorphinoutdoor.com/wp-content/uploads/2020/09/wavesHunters-scaled.jpg",
        "https://endorphinoutdoor.com/wp-content/uploads/2020/09/mare-monti-e-gabbiano-scaled.jpg",
        "https://endorphinoutdoor.com/wp-content/uploads/2020/09/bracco-scaled.jpg",
      ],
      backgrounds_dx: [
        "https://endorphinoutdoor.com/wp-content/uploads/2020/09/cavalli-corsa-scaled.jpg",
        "https://endorphinoutdoor.com/wp-content/uploads/2020/10/DSCF2935-scaled.jpg",
        "https://endorphinoutdoor.com/wp-content/uploads/2020/06/WORDPRESSIMG-1.gif",
      ],
      img_sx: null,
      img_dx: null,
    };
  },
  created() {
    console.log(window.innerWidth);
    this.$store.commit("isMetamorphosis", true);
    this.setSplash();
    this.backImgs();
    this.$store.commit("selectEl", "mHome");
    setTimeout(() => {
      this.$store.commit("toggleHomeMenuColor", false);
    }, 2000);
  },
  methods: {
    pushAboutM() {
      this.pushedAbout = true;
      this.$router.push({
        name: "mWeAre",
        // params: { eventName: this.event.name },
      });
    },
    pushBlogM() {
      this.pushedBlog = true;
      this.$router.push({
        name: "mWeTell",
        // params: { eventName: this.event.name },
      });
    },
    pushContentsM() {
      this.pushedContents = true;
      this.$router.push({
        name: "mWeDo",
        // params: { eventName: this.event.name },
      });
    },
    pushAbout() {
      this.pushedAbout = true;
      setTimeout(() => {
        this.$router.push({
          name: "mWeAre",
          // params: { eventName: this.event.name },
        });
      }, 2000);
    },
    pushBlog() {
      this.pushedBlog = true;
      setTimeout(() => {
        this.$router.push({
          name: "mWeTell",
          // params: { eventName: this.event.name },
        });
      }, 2000);
    },
    pushContents() {
      this.pushedContents = true;
      setTimeout(() => {
        this.$router.push({
          name: "mWeDo",
          // params: { eventName: this.event.name },
        });
      }, 2000);
    },
    setSplash() {
      setTimeout(() => {
        this.splash = false;
      }, 4000);
    },
    backImgs() {
      setInterval(() => {
        this.img_sx = this.backgrounds_sx[
          Math.floor(Math.random() * this.backgrounds_sx.length)
        ];
        this.img_dx = this.backgrounds_dx[
          Math.floor(Math.random() * this.backgrounds_dx.length)
        ];
      }, 1000);
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
  height: 100vh;
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
  align-items: flex-start;
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
    color: white;
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
}
@media (max-width: 600px) {
  .canvasBoxBabylon {
    height: 100vh;
    width: 100vw;
  }
  .splashText {
    left: calc(50vw - 125px);
  }
}
</style>