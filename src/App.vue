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
      activeIndex: 0,
      left: 0,
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
      if (val - this.xStart < -50) {
        if (this.activeIndex !== this.items.length - 1) {
          this.next();
        } else {
          this.$refs.inner.style.left =
            -(this.step * (this.items.length - 1)) + "px";
        }
      } else if (val - this.xStart > 50) {
        if (this.activeIndex !== 0) {
          this.prev();
        } else {
          this.$refs.inner.style.left = this.left;
        }
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
        this.resetTranslate();
        this.transitioning = false;
      });
    },
    moveLeft() {
      const currIndex = Math.abs(this.left) / this.step;
      const transform = this.step - (this.left % this.step);
      this.activeIndex = currIndex + 1;
      this.left = -this.activeIndex * this.step;
      this.$refs.inner.style.left = `${this.left}px`;
      this.innerStyles = {
        transform: `translateX(-${transform}px)`,
      };
    },
    moveRight() {
      const currIndex = Math.abs(this.left) / this.step;
      const transform = this.step + (this.left % this.step);
      this.activeIndex = currIndex - 1;
      this.left = -this.activeIndex * this.step;
      this.$refs.inner.style.left = `${this.left}px`;
      this.innerStyles = {
        transform: `translateX(${transform}px) `,
      };
    },

    handleDragStart(e) {
      this.xStart = e.changedTouches[0].clientX;
    },
    handleDragMove(e) {
      this.xCurr = this.xStart;
      this.drag = e.changedTouches[0].clientX - this.xCurr;
      this.xCurr = e.changedTouches[0].clientX;
      this.$refs.inner.style.left = `${this.left + this.drag}px`;
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
  transition: transform 0.5s;
  white-space: nowrap;
  position: relative;
}
</style>
