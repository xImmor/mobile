<template>
  <div id="home">
    <nav-bar class="home-nav">
      <template v-slot:center>
        <div class="title-item">
          购物街
        </div>
      </template>
    </nav-bar>
    <tab-control ref="tabControl1" :titles="tabControlText" @tabClick="tabClick" class="tab-control" v-show="isTabFixed"/>
    <scroll class="content"
            ref="scroll"
            :probe-type="3"
            @position="backTop"
            :pull-up-load="true"
            @pullingUp="loadMoreProduct">
      <home-swiper :banners="banners" @swiperImgLoad="calcTabOffsetTop"/>
      <HomeRecommend :recommends="recommends" @recommendImgLoad="calcTabOffsetTop"/>
      <home-feature @featureImgLoad="calcTabOffsetTop"/>
      <tab-control ref="tabControl2" :titles="tabControlText" @tabClick="tabClick"/>
      <product-list :products="showProducts" @itemImageLoad="itemImageLoad"/>
    </scroll>
    <back-top @click="backTopClick" v-show="isShowBackTop"/>
  </div>
</template>

<script>
import Scroll from "@/components/common/scroll/Scroll";
import NavBar from "@/components/common/navbar/NavBar";
import TabControl from "@/components/context/tabControl/TabControl";
import ProductList from "@/components/context/productList/ProductList";
import BackTop from "@/components/context/backTop/BackTop";

import HomeSwiper from "@/views/home/homeChildComps/HomeSwiper";
import HomeRecommend from "@/views/home/homeChildComps/HomeRecommend";
import HomeFeature from "@/views/home/homeChildComps/HomeFeature";
import {getHomeMultidata, getHomeProducts} from "@/network/home";

export default {
  name: "Home",
  components: {TabControl, HomeFeature, HomeRecommend, NavBar, HomeSwiper, ProductList, Scroll, BackTop},
  data() {
    return {
      banners: [],
      recommends: [],
      tabControlText: ['流行', '新款', '精选'],
      products: {
        "pop": {
          page: 0,
          list: []
        },
        "new": {
          page: 0,
          list: []
        },
        "sell": {
          page: 0,
          list: []
        }
      },
      currentType: 'pop',
      isShowBackTop: false,
      tabOffsetTop: 0,
      isTabFixed: false
    }
  },
  computed: {
    showProducts() {
      return this.products[this.currentType].list;
    }
  },
  created() {
    this.getHomeMultidata();
    this.getHomeProducts('pop');
    this.getHomeProducts('new');
    this.getHomeProducts('sell');
  },
  methods: {
    /**
     * 网络请求
     */
    getHomeMultidata() {
      getHomeMultidata().then(res => {
        this.banners = res.data.banner.list;
        this.recommends = res.data.recommend.list;
      });
    },

    getHomeProducts(type) {
      const page = this.products[type].page + 1;
      getHomeProducts(type, page).then(res => {
        this.products[type].list.push(...res.data.list);
        this.products[type].page += 1;
        this.$refs.scroll.finishPullUp();
      });
    },

    /**
     * 事件监听
     */
    calcTabOffsetTop() {
      this.tabOffsetTop = this.$refs.tabControl2.$el.offsetTop;
    },

    tabClick(index) {
      switch (index) {
        case 0:
          this.currentType = 'pop';
          break;
        case 1:
          this.currentType = 'new';
          break;
        case 2:
          this.currentType = 'sell';
          break;
      }
      this.$refs.tabControl1.currentIndex = index;
      this.$refs.tabControl2.currentIndex = index;
    },

    itemImageLoad() {
      this.$refs.scroll.refresh();
    },

    backTopClick() {
      this.$refs.scroll.scrollTo(0, 0, 500);
    },

    backTop(position) {
      this.isShowBackTop = (-position.y) > 1000;

      this.isTabFixed = (-position.y) > this.tabOffsetTop;
    },

    loadMoreProduct() {
      this.getHomeProducts(this.currentType);
    }
  }
}
</script>

<style scoped>
#home {
  height: 100vh;
  position: relative;
}

.home-nav {
  background-color: var(--color-tint);
  color: #f6f6f6;
  /*position: fixed;*/
  /*left: 0;*/
  /*right: 0;*/
  /*top: 0;*/
  /*z-index: 9;*/
}

.title-item {
  flex: 1;
}

.tab-control {
  position: relative;
  z-index: 9;
}

.content {
  position: absolute;
  overflow: hidden;
  top: 44px;
  left: 0;
  right: 0;
  bottom: 49px;
}
</style>
