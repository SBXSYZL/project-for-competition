<template>
  <div>
    <div class="main-wrap">
      <!--头部 start-->
      <div class="header-height">
        <Header class="my-header"/>
      </div>
      <!--头部 end-->
      <!--body start-->
      <div class="body-div">
        <router-view/>
      </div>
      <!--body end-->
      <!--footer start-->
      <div
        class="footer-div">
        <p>@2020-3-3 FuRong#1-204 created 厦门大学嘉庚学院 信息科学与技术学院</p>
        <p>2017级 软件工程 叶泽林 - we said: we are compiler</p>
      </div>
      <!--footer end-->
    </div>
  </div>

</template>

<script>
  import $ from 'jquery'
  import Header from '../components/Header'
  import SideBar from '../components/SideBar'
  import Bus from '../assets/js/bus.js'

  export default {
    name: 'Home',
    components: {
      SideBar,
      Header
    },
    data () {
      return {
        minHeight: '600px'
      }
    },
    methods: {
      getMinHeight () {
        let height = window.innerHeight - 200
        this.minHeight = height + 'px'
      }
    },
    created () {
      this.getMinHeight()
    }
  }

</script>

<style scoped>
  .header-height {
    height: 20%
  }

  .my-header {
    margin-top: 2%
  }

  .body-div {
    height: 80%;
    min-height: 800px;
    text-align: center;
  }

  .footer-div {
    text-align: center;
    width: 100%;
    background: #f84e4e;
    height: 10%;
    margin-top: 5%;
    min-height: 40px;
    padding-top: 10px;
    padding-bottom: 5px
  }

</style>

