<template>
  <div class="mWorksBox">
    <div class="wFilters">
      <div
        class="wFi"
        :class="[
          {
            catSelectedWorks: 'photo_video' == catSelected,
          },
        ]"
        @click="filterTax(48, 'categories', 'photo_video')"
      >
        PHOTO/VIDEO
      </div>
      <div
        class="wFi"
        :class="[
          {
            catSelectedWorks: 'dev' == catSelected,
          },
        ]"
        @click="filterTax(47, 'categories', 'dev')"
      >
        DEV
      </div>
      <div
        class="wFi"
        :class="[
          {
            catSelectedWorks: 'graphics' == catSelected,
          },
        ]"
        @click="filterTax(49, 'categories', 'graphics')"
      >
        GRAPHICS
      </div>
    </div>
    <q-circular-progress
      indeterminate
      size="45px"
      :thickness="1"
      color="grey-8"
      track-color="blue-grey-14"
      class="q-ma-md loaderWorks"
      v-if="!works"
    />
    <div
      class="work"
      v-for="(work, i) in works"
      :key="i + `_work`"
      @click="pushSingleWork(work.id)"
      :class="[
        {
          pushedSingleWorkClass: pushedSingleWork,
        },
      ]"
    >
      <v-img
        v-if="work.featured_image_src"
        :src="work.featured_image_src"
        class="grey lighten-2 workImage"
        :aspect-ratio="16 / 9"
        @mouseover="showByIndex = i"
        @mouseout="showByIndex = null"
      >
        <template v-slot:placeholder>
          <v-row class="fill-height ma-0" align="center" justify="center">
            <v-progress-circular
              indeterminate
              color="grey lighten-5"
            ></v-progress-circular>
          </v-row>
        </template>
        <div
          class="workImageOverlay"
          :class="[
            {
              'fade-out-worktitle': showByIndex !== i,
              'fade-in': showByIndex === i,
            },
          ]"
        ></div>
        <div
          class="imgTitle"
          :class="[
            {
              'fade-out-worktitle': showByIndex !== i,
              'fade-in': showByIndex === i,
            },
          ]"
        >
          {{ work.title.rendered }}
        </div>
      </v-img>
    </div>
  </div>
</template>
<script>
import { db, Timestamp, GeoPoint } from "../../main";
import firebase from "firebase";
import VueMoment from "vue-moment";
import { mapState, mapGetters } from "vuex";
import axios from "axios";
import _ from "lodash";

export default {
  data() {
    return {
      works: null,
      catSelected: "",
      env: process.env.VUE_APP_DB_ENV,
      adminCode: null,
      loaded: false,
      showByIndex: null,
      pushedSingleWork: false,
    };
  },
  created() {
    this.getWorks();
    this.$store.commit("selectEl", "mContents");
    setTimeout(() => {
      this.$store.commit("toggleHomeMenuColor", true);
    }, 2000);
  },
  methods: {
    pushSingleWork(workId) {
      this.pushedSingleWork = true;
      setTimeout(() => {
        this.$router.push({
          name: "mSingleWork",
          params: { id: workId },
        });
      }, 2000);
    },
    async getWorks() {
      this.loaded = false;
      try {
        var works = (
          await axios.get(
            `https://endorphinoutdoor.com/wp-json/wp/v2/posts?categories=46`
          )
        ).data;
        await this.getOtherFields(works);
      } catch (error) {
        console.log(error);
      }

      this.loaded = true;
    },
    async getOtherFields(works) {
      // get other fields
      for (const work of works) {
        // non piu necessario #######
        // if (work.featured_media !== 0) {
        //   work["featured_media_url"] = (
        //     await axios.get(
        //       `https://endorphinoutdoor.com/wp-json/wp/v2/media/${work.featured_media}`
        //     )
        //   ).data.source_url;
        // }
        // add custom field
        // var acf = (
        //   await axios.get(
        //     `https://endorphinoutdoor.com/wp-json/acf/v3/posts/${work.id}`
        //   )
        // ).data.acf;
        // work["acf"]["client_name"] = acf.client_name;
        // work["acf"]["year"] = acf.year;
        // work["acf"]["technologies"] = acf.technologies;
      }
      this.works = works;
    },
    async filterTax(tax, type, catSel) {
      this.catSelected = catSel;
      this.works = null;
      this.loaded = false;
      try {
        var works = (
          await axios.get(
            `https://endorphinoutdoor.com/wp-json/wp/v2/posts?${type}=${tax}`
          )
        ).data;
        await this.getOtherFields(works);
      } catch (error) {
        console.log(error);
      }
      this.loaded = true;
    },
  },
  computed: {
    ...mapState({
      user: "user",
      userRole: "userRole",
      menuEl: "menuEl",
    }),
  },
};
</script>
<style lang="scss">
@import "../../sass/_variables.scss";
.fade-out-worktitle {
  -webkit-animation: fade-out 2s ease-out both;
  animation: fade-out 2s ease-out both;
}
.pushedSingleWorkClass {
  opacity: 0;
  transition: 3s;
}
.mWorksBox {
  width: 100vw;
  min-height: 100vh;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  flex-direction: column;
  padding: 100px 0;
}
.work {
  width: 100%;
  height: 300px;
  cursor: pointer;
}
.workImage {
  width: 100%;
  max-height: 100%;
  transition: 1s;
  position: relative;
}
.workImageOverlay {
  width: 100%;
  height: 100%;
  position: absolute;
  top: 0;
  left: 0;
  background: rgba(51, 51, 51, 0.575);
}
.imgTitle {
  transition: 1s;
  position: absolute;
  top: 80%;
  right: 12%;
  text-align: right;
  font-size: 27px;
  color: white;
}
.wFilters {
  width: 100%;
  height: 50px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.wFi {
  width: calc(100vw / 3);
  height: 100%;
  border: 1px solid rgba(223, 223, 223, 0.63);
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  transition: 1s;

  &:hover {
    background: rgba(182, 182, 182, 0.521);
    transition: 1s;
  }
}
.loaderWorks {
  margin-top: 30vh !important;
}
.catSelectedWorks {
  background: $primary-color;
  transition: 1s;
  padding: 3px;
}
// ##
@media (max-width: 650px) {
  .work {
    height: 200px;
  }
  .wFilters {
    flex-direction: column;
    height: 150px;
  }
  .wFi {
    width: 100%;
    height: 50px;
  }
  .imgTitle {
    transition: 1s;
    position: absolute;
    top: 80%;
    right: 8%;
    text-align: right;
    font-size: 16px;
    color: white;
  }
}
// ##
@media (max-width: 400px) {
  .work {
    height: 150px;
  }
}
</style>