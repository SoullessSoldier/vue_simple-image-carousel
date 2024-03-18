<template>
  <div class="card-carousel">
    <div
      class="image-wrapper"
      @mouseover="stopTimer"
      @mouseleave="restartTimer"
    >
      <div class="card-img">
        <div class="progressbar" v-if="autoSlideInterval && showProgressBar">
          <div :style="{ width: progressBar + '%' }"></div>
        </div>
        <img class="card-img-image" :src="currentImage" alt="" />
      </div>
      <!-- <div class="actions"> -->
      <div @click="prevImage" class="control prev">&#8249;</div>
      <div @click="nextImage" class="control next">&#8250;</div>
      <!-- </div> -->
    </div>
    <div class="thumbnails">
      <div
        v-for="(image, index) in images"
        :key="image.id"
        :class="['thumbnail-image', activeImage == index ? 'active' : '']"
        @click="activateImage(index)"
      >
        <img :src="image.thumb" />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted } from "vue";

/* Start conditions */
const startingImage = 0;
const autoSlideInterval = 4000;
const showProgressBar = true;

const activeImage = ref(0);
const autoSlideTimeout = ref<any>();
//If the timer is stopped e.g. when hovering over the carousel
const stopSlider = ref(false);
//Hold the time left until changing to the next image
const timeLeft = ref(0);
//Hold the interval so we can clear it when needed
const timerInterval = ref<any>();
//Every 10ms decrease the timeLeft
const countdownInterval = ref(10);
const images = [
  {
    id: "1",
    big: "/images/p1.jpg",
    thumb: "/images/thumbs/p1.jpg",
  },
  {
    id: "2",
    big: "/images/p2.jpg",
    thumb: "/images/thumbs/p2.jpg",
  },
  {
    id: "3",
    big: "/images/p3.jpg",
    thumb: "/images/thumbs/p3.jpg",
  },
  {
    id: "4",
    big: "/images/p4.avif",
    thumb: "/images/thumbs/p4.jpg",
  },
  {
    id: "5",
    big: "/images/p1.jpg",
    thumb: "/images/thumbs/p1.jpg",
  },
  {
    id: "6",
    big: "/images/p2.jpg",
    thumb: "/images/thumbs/p2.jpg",
  },
  {
    id: "7",
    big: "/images/p3.jpg",
    thumb: "/images/thumbs/p3.jpg",
  },
  {
    id: "8",
    big: "/images/p4.avif",
    thumb: "/images/thumbs/p4.jpg",
  },
];

const image = document.querySelector(".card-img-image") as HTMLImageElement;

const currentImage = computed(() => images[activeImage.value].big);

const progressBar = computed(() => {
  //Calculate the width of the progressbar
  return 100 - (timeLeft.value / autoSlideInterval) * 100;
});

const nextImage = () => {
  let active = activeImage.value + 1;
  if (active >= images.length) {
    active = 0;
  }
  activateImage(active);
};
// Go backwards on the images array
// or go at the last image
const prevImage = () => {
  let active = activeImage.value - 1;
  if (active < 0) {
    active = images.length - 1;
  }
  activateImage(active);
};
const activateImage = (imageIndex: number) => {
  activeImage.value = imageIndex;
};

//Wait until 'interval' and go to the next image;
const startTimer = (interval: any) => {
  if (interval && interval > 0 && !stopSlider.value) {
    clearTimeout(autoSlideTimeout.value);
    autoSlideTimeout.value = setTimeout(function () {
      nextImage();
      startTimer(autoSlideInterval);
    }, interval);
  }
};
//Stop the timer when hovering over the carousel
const stopTimer = () => {
  clearTimeout(autoSlideTimeout.value);
  stopSlider.value = true;
  clearInterval(timerInterval.value);
};
//Restart the timer(with 'timeLeft') when leaving from the carousel
const restartTimer = () => {
  stopSlider.value = false;
  clearInterval(timerInterval.value);
  startCountdown();
  startTimer(timeLeft.value);
};
//Start countdown from 'autoSlideInterval' to 0
const startCountdown = () => {
  if (!showProgressBar) return;
  timerInterval.value = setInterval(function () {
    timeLeft.value -= countdownInterval.value;
    if (timeLeft.value <= 0) {
      timeLeft.value = autoSlideInterval;
    }
  }, countdownInterval.value);
};

onMounted(() => {
  if (startingImage && startingImage >= 0 && startingImage < images.length) {
    activeImage.value = startingImage;
  }
  if (autoSlideInterval && autoSlideInterval > countdownInterval.value) {
    //Start the timer to go to the next image
    startTimer(autoSlideInterval);
    timeLeft.value = autoSlideInterval;
    //Start countdown to show the progressbar
    startCountdown();
  }
});
</script>

<style scoped>
.card-carousel {
  user-select: none;
  position: relative;
}

.image-wrapper {
  margin: 0 auto;
  width: 70%;
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}

.progressbar {
  display: block;
  width: 100%;
  height: 5px;
  position: absolute;
  background-color: rgba(221, 221, 221, 0.25);
  z-index: 1;
}

.progressbar > div {
  background-color: rgba(255, 255, 255, 0.52);
  height: 100%;
}

.thumbnails {
  display: flex;
  justify-content: space-evenly;
}

.thumbnail-image {
  display: flex;
  align-items: center;
  cursor: pointer;
  padding: 2px;
}

.thumbnail-image > img {
  width: 100%;
  height: auto;
  transition: all 250ms;
  opacity: 0.6;
}

.thumbnail-image:hover > img,
.thumbnail-image.active > img {
  opacity: 1;
  box-shadow: 2px 2px 6px 1px rgba(0, 0, 0, 0.5);
}

.card-img {
  position: relative;
  width: auto;
  margin-bottom: 15px;
}

.card-img > img {
  display: block;
  margin: 0 auto;
  height: 55vh;
  /* width: 100%; */
  object-fit: contain;
}

/* .actions {
  font-size: 1.5em;
  height: 40px;
  position: absolute;
  top: 50%;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  color: #585858;
} */

.control {
  position: absolute;
  width: 50px;
  height: 50px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 36px;
  line-height: 0;
  color: white;
  background-color: blue;
  border-radius: 50%;
  transform: translateY(calc(50% - 1.5em / 2));
  cursor: pointer;
  transition: all 250ms;
}

.control.prev {
  margin-left: 5px;
  left: 0;
}

.control.next {
  margin-right: 5px;
  right: 0;
}

.control:hover {
  color: #eee;
}
</style>
