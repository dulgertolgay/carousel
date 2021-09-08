<template>
  <div ref="carousel" class="carousel">
    <div
      @touchstart="handleDragStart"
      @touchmove="handleDragMove"
      @touchend="handleDragEnd"
      class="inner"
      ref="inner"
      :style="innerStyles"
    >
      <carousel-item v-for="item in items" :key="item.index" :msg="item.msg" />
    </div>
  </div>
</template>

<script>
import CarouselItem from "./components/CarouselItem.vue";

export default {
  name: "App",
  components: {
    CarouselItem,
  },
  computed: {},
  data() {
    return {
      drag: 0,
      xStart: null,
      xCurr: null,
      xEnd: null,
      innerStyles: {},
      step: null,
      totalTransition: 0,
      transitioning: false,
      items: [
        {
          index: 1,
          msg: "1st Carousel",
        },
        {
          index: 2,
          msg: "2nd Carousel",
        },
        {
          index: 3,
          msg: "3rd Carousel",
        },
        {
          index: 4,
          msg: "4th Carousel",
        },
        {
          index: 5,
          msg: "5th Carousel",
        },
      ],
    };
  },

  mounted() {
    this.setStep();
    this.resetTranslate();
  },
  watch: {
    xEnd(val) {
      if (val - this.xStart < -100) {
        this.next();
      } else if (val - this.xStart > 100) {
        this.prev();
      }
    },
  },
  methods: {
    setStep() {
      const innerWidth = this.$refs.inner.scrollWidth;
      const totalCards = this.items.length;
      this.step = innerWidth / totalCards; //gives width of a card + left-right margins
    },
    next() {
      if (this.transitioning) {
        return;
      }
      this.transitioning = true;
      this.moveLeft();
      this.afterTransition(() => {
        const card = this.items.shift();
        this.items.push(card);
        this.resetTranslate();
        this.transitioning = false;
      });
    },
    prev() {
      if (this.transitioning) {
        return;
      }
      this.transitioning = true;
      this.moveRight();
      this.afterTransition(() => {
        const card = this.items.pop();
        this.items.unshift(card);
        this.resetTranslate();
        this.transitioning = false;
      });
    },
    moveLeft() {
      this.innerStyles = {
        transform: `translateX(-${this.step + this.drag}px)
                    translateX(-${this.step}px)`,
      };
    },
    moveRight() {
      this.innerStyles = {
        transform: `translateX(${this.step - this.drag}px)
                     translateX(-${this.step}px)`,
      };
    },

    handleDragStart(e) {
      this.xStart = e.changedTouches[0].clientX;
    },
    handleDragMove(e) {
      this.xCurr = this.xStart;
      this.drag = e.changedTouches[0].clientX - this.xCurr;
      this.xCurr = e.changedTouches[0].clientX;
      const inner = this.$refs.inner;
      inner.style.left = this.drag + "px";
    },
    handleDragEnd(e) {
      this.xEnd = e.changedTouches[0].clientX;
    },

    afterTransition(callback) {
      const listener = () => {
        callback();
        this.$refs.inner.removeEventListener("transitionend", listener);
      };
      this.$refs.inner.addEventListener("transitionend", listener);
    },
    resetTranslate() {
      this.innerStyles = {
        transition: "none",
        transform: `translateX(-${this.step}px)`,
      };
    },
  },
};
</script>

<style>
.carousel {
  padding: 2rem;
  width: 300px;
  overflow: hidden;
}
.inner {
  transition: transform 0.2s;
  white-space: nowrap;
  position: relative;
  left: 0;
}
</style>
