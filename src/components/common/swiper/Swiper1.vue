<template>
  <div id="hy-swiper">
    <div class="swiper" @touchstart="touchStart" @touchmove="touchMove" @touchend="touchEnd">
      <slot></slot>
    </div>

    <div class="indicator">
      <div v-for="(item, index) in imgs" class="indi-item" :class="{active: index === num-1}"
           :key="index"></div>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Swiper",
    props: {
      interval: {
        type: Number,
        default: 3000  //轮播速度
      },
      animDuration: {
        type: Number,
        default: 300  //移动时间
      },
      moveRatio: {
        type: Number,
        default: 0.25  //手指触发比率
      },
      showIndicator: {
        type: Boolean,
        default: true
      }
    },
    data() {
      return {
        slideCount: 0, // 元素个数
        w: 0, // swiper的宽度
        swiperStyle: {}, // swiper样式
        num: 1, // 当前的index
        scrolling: false, // 是否正在滚动
        swiper: null,
        imgs:0
      }
    },
    mounted() {
      // 1.操作DOM, 在前后添加Slide
      this.handleDom();
      // 2.开启定时器
      this.update()
      this.startTimer();
    },
    methods: {
      /**
       * 定时器操作
       */
      startTimer() {
        this.time = setInterval(() => {
          this.num++
          this.update()
        }, 2000)
      },
      stopTimer() {
        clearInterval(this.time);
      },

      /**
       * 滚动到正确的位置
       */
      /**
       * 校验正确的位置
       */
      update() {
        this.swiperStyle.transform = `translateX(${-this.num * this.w + 'px'})`
        this.swiperStyle.transition = 'all .3s'
        this.swiper.addEventListener('transitionend', () => {
          if (this.num >= this.imgs) {
            this.swiperStyle.transition = 'none'
            this.num = 0
            this.swiperStyle.transform = `translateX(${-this.num * this.w + 'px'})`
          } else if (this.num <= 0) {
            this.swiperStyle.transition = 'none'
            this.num = this.imgs
            this.swiperStyle.transform = `translateX(${-this.num * this.w + 'px'})`
          }
        })
        if (this.num>= this.imgs){

        }
      },

      /**
       * 操作DOM, 在DOM前后添加Slide
       */
      handleDom() {
        // 1.获取要操作的元素
        this.swiper = document.querySelector('.swiper');
        let img=this.swiper.children
        // 2.保存个数
        this.imgs = img.length;
        // 3.如果大于1个, 那么在前后分别添加一个slide
        if (this.imgs > 1) {
          let cloneFirst = img[0].cloneNode(true);
          let cloneLast = img[this.imgs - 1].cloneNode(true);
          this.swiper.insertBefore(cloneLast,img[0]);
          this.swiper.appendChild(cloneFirst);
          this.w = this.swiper.offsetWidth;
          this.swiperStyle = this.swiper.style;
        }

        // 4.让swiper元素, 显示第一个(目前是显示前面添加的最后一个元素)
      },

      /**
       * 拖动事件的处理
       */
      touchStart(e) {

        // 2.停止定时器
        this.stopTimer();

        // 3.保存开始滚动的位置
        this.startX = e.touches[0].pageX;
      },

      touchMove(e) {
        // 1.计算出用户拖动的距离
        this.currentX = e.touches[0].pageX;
        this.distance = this.currentX - this.startX;
        let currentPosition = -this.num * this.w;
        let moveDistance = this.distance + currentPosition;
        // 2.设置当前的位置
        this.swiperStyle.transform=`translateX(${moveDistance + 'px'})`;
      },

      touchEnd(e) {
        // 1.获取移动的距离
        let currentMove = Math.abs(this.distance);

        // 2.判断最终的距离
        if (this.distance > 0 && currentMove > this.w * this.moveRatio) { // 右边移动超过0.5
          this.num--
        } else if (this.distance < 0 && currentMove > this.w * this.moveRatio) { // 向左移动超过0.5
          this.num++
        }
        this.update();
        console.log(this.num)
        // 3.移动到正确的位置
        this.swiperStyle.transition = 'all .3s'


        // 4.移动完成后重新开启定时器
        this.startTimer();
      }
    }
  }
</script>

<style scoped>
  #hy-swiper {
    overflow: hidden;
    position: relative;
  }

  .swiper {
    display: flex;
  }

  .indicator {
    display: flex;
    justify-content: center;
    position: absolute;
    width: 100%;
    bottom: 8px;
  }

  .indi-item {
    box-sizing: border-box;
    width: 8px;
    height: 8px;
    border-radius: 4px;
    background-color: #fff;
    line-height: 8px;
    text-align: center;
    font-size: 12px;
    margin: 0 5px;
  }

  .indi-item.active {
    background-color: rgba(212, 62, 46, 1.0);
  }
</style>
