<template>
  <div v-show="loaded" ref="zucksAd" >
    <div :style="`width: ${adResponse.w}px;height: ${adResponse.h}px; margin: auto;`">
      <div style="position: relative">
        <div ref="zucksInfoDiv" class="zucks-info">
          <a
            :href="adResponse.fb"
            style="width:100%;height:100%;border:0;margin:0;padding:0;display:none;"
            target="_blank"
            rel="noopener"
            ref="zucksInfoA"
          ></a>
        </div>
      </div>
      <a
        @click="click"
        :href="adResponse.r"
        style="border:0;margin:0;padding:0;"
        target="_blank"
        rel="noopener"
      >
        <img :src="adResponse.iu" alt="" :width="adResponse.w" :height="adResponse.h"/>
      </a>
    </div>
  </div>
</template>

<script lang="ts">
  import {Component, Emit, Prop, Vue} from 'vue-property-decorator';
  import axios from 'axios'

  @Component
  export default class HelloWorld extends Vue {
    @Prop() private frameId!: string;
    @Emit() private click(){}

    private adResponse: IResponse;
    private loaded = false;
    private infoClicked = false;
    private viewed = false;

    private async created () {
      const rnd = Math.floor(1E9 * Math.random());
      const s = screen || { width: 0, height: 0 };
      const sw = s.width;
      const sh = s.height;
      const touch = 'ontouchstart' in window ? '1' : '0';
      const res = await axios.get(`https://sh.zucks.net/opt/json/api/v2?f=${this.frameId}&rnd=${rnd}&sw=${sw}&sh=${sh}&touch=${touch}`);
      this.adResponse = res.data as IResponse;
      this.loaded = true
    }

    private mounted() {
      const zucksInfoDiv = this.$refs.zucksInfoDiv as HTMLElement;
      const zucksInfoA = this.$refs.zucksInfoA as HTMLElement;
      let e = () => {
        zucksInfoDiv.removeEventListener("click", e);
        this.infoClicked ? zucksInfoDiv.style.width = "17px" : (zucksInfoDiv.style.webkitTransitionProperty = "width",
          zucksInfoDiv.style.webkitTransitionDuration = "0.5s",
          zucksInfoDiv.style.width = "85px")
      };
      zucksInfoDiv.addEventListener("click", e);
      zucksInfoDiv.addEventListener("webkitTransitionEnd", () => {
        zucksInfoA.style.display = this.infoClicked ? "none" : "block";
        this.infoClicked = !this.infoClicked;
        zucksInfoDiv.addEventListener("click", e)
      });

      const observer = new IntersectionObserver(() => {
        if (!this.viewed && this.loaded) {
          axios.get(this.adResponse.vi);
          this.viewed = true
        }
      });
      observer.observe(this.$refs.zucksAd as HTMLElement)
    }
  }
</script>

<style scoped>
  .zucks-info {
    width:17px;
    height:15px;
    background:rgba(255, 255, 255, 0.8) url('https://static.zucks.net.zimg.jp/i/ads_by.png') no-repeat right top;
    background-size:85px 15px;
    position:absolute;
    top: 0;
    right: 0;
    margin: 0;
    border-bottom-left-radius:4px;
  }
</style>
